emsi/docker-gen based on https://github.com/jwilder/docker-gen

*Usage*

A sample usage of docker-gen with nginx as reverse proxy.

### Create nginx-data (data-only container for nginx reverse proxy)
This container holds `/etc/nginx` volume
```
# create nginx-data container
docker run -ti --name nginx-data emsi/nginx-data
# enter the /etc/nginx volume
cd  $(docker inspect nginx-data | grep '"/etc/nginx": "' | cut -f4 -d'"')
# run original nginx
docker run -d --name nginx-orig -t nginx
# copy config files from original nginx
docker cp nginx-orig:/etc/nginx .
mv nginx/* .
rmdir  nginx
cd
# remove original nginx
docker rm -f nginx-orig
```

### Create nginx reverse proxy 
This nginx takes config from ndinx-data container
```
docker run -d -p 80:80 --name nginx --volumes-from nginx-data -t nginx
```

### Create docker-gen-data (data-only container for docker-gen)
This container holds `/etc/docker-gen' volume
```
# create docker-gen-data container
docker run -ti --name docker-gen-data emsi/docker-gen-data
# enter the /etc/docker-gen volume
cd $(docker inspect docker-gen-data  | grep '"/etc/docker-gen": "' | cut -f4 -d'"')
# download and install nginx docker-gen template
curl -o docker-gen.cfg https://raw.githubusercontent.com/emsi/Docker/master/docker-gen/docker-gen.cfg
mkdir templates
cd templates
curl -o nginx.tmpl https://raw.githubusercontent.com/jwilder/nginx-proxy/master/nginx.tmpl
cd
```
At this point you should install your own template and/or docker-gen config files to implement your setup.

### Start docker-gen
This container uses template from docker-gen-data volume and generates config to nginx-data volume (which is ultimately used by nginx reverse proxy)
```
docker run -d --name docker-gen --volumes-from nginx-data --volumes-from docker-gen-data -v /var/run/docker.sock:/var/run/docker.sock -t emsi/docker-gen -config /etc/docker-gen/docker-gen.cfg
```
When started you may check its log and shoud see:
```
# docker logs docker-gen
2015/07/01 14:56:13 Generated '/etc/nginx/conf.d/default.conf' from 2 containers
2015/07/01 14:56:13 Sending container 'nginx' signal '1'
2015/07/01 14:56:13 Watching docker events
```

### Register an app to rev-proxy
This will register a tenant1.example.com host to reverse proxy based on the template used above.
```
docker run -d -e VIRTUAL_HOST=tenant1.example.com --name tenant1 nginx
```
Or any other app you like:
```
docker run -d -h tenant1.example.com -e VIRTUAL_HOST=tenant1.example.com --name tenant1-test training/webapp
docker run -d -h tenant2.example.com -e VIRTUAL_HOST=tenant2.example.com --name tenant2-joomla gjong/apache-joomla
docker run -d -h tenant3.example.com -e VIRTUAL_HOST=tenant3.example.com --name tenant3-owncloud dperson/owncloud
```
etc...

If it works properly you should see something like this in docker-gen logs:
```
# docker logs docker-gen
2015/07/01 14:56:13 Generated '/etc/nginx/conf.d/default.conf' from 2 containers
2015/07/01 14:56:13 Sending container 'nginx' signal '1'
2015/07/01 14:56:13 Watching docker events
2015/07/01 14:56:40 Received event start for container 27266157b1b6
2015/07/01 14:56:40 Generated '/etc/nginx/conf.d/default.conf' from 3 containers
2015/07/01 14:56:40 Sending container 'nginx' signal '1'
```
Note the changing number of containers (2 then 3).
