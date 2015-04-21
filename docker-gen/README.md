emsi/docker-gen based on https://github.com/jwilder/docker-gen

*Usage*

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
# remove original nginx
docker rm -f nginx-orig
```

### Create nginx reverse proxy 
This nginx takes config from ndinx-data container
```
docker run -d -p 80:80 --name nginx --volumes-from nginx-data -t nginx
```
*WARNING* This container does NOT listen on port 80 as nginx has an empty config at this moment. It will start to forward connections once we register an app to it.

### Create nginx-gen-data (data-only container for docker-gen)
This container holds `/etc/docker-gen' volume
```
# create nginx-gen-data container
docker run -ti --name nginx-gen-data emsi/docker-gen-data
# enter the /etc/docker-gen/templates volume
cd $(docker inspect nginx-gen-data  | grep '"/etc/docker-gen": "' | cut -f4 -d'"')
# download and install nginx docker-gen template
mkdir templates
cd templates
curl -o nginx.tmpl https://raw.githubusercontent.com/jwilder/docker-gen/master/templates/nginx.tmpl
```
At this point you should install your own template and/or docker-gen config files to implement your setup.

### Start docker-gen
This container uses template from nginx-gen-data volume and generates config to nginx-data volume (which is ultimately used by nging reverse proxy)
```
docker run -d --name nginx-gen --volumes-from nginx-data --volumes-from nginx-gen-data -v /var/run/docker.sock:/var/run/docker.sock -t emsi/docker-gen -notify-sighup nginx -watch /etc/docker-gen/templates/nginx.tmpl /etc/nginx/conf.d/default.conf
```

### Register an app to rev-proxy
This will register a tenant1.example.com host to reverse proxy based on the template used above.
```
docker run -d -e VIRTUAL_HOST=tenant1.example.com --name tenant1 nginx
```
