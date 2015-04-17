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

### Create nginx-gen-data (data-only container for docker-gen)
This container holds `/etc/docker-gen/templates' volume
```
# create nginx-gen-data container
docker run -ti --name nginx-gen-data emsi/docker-gen-data
# enter the /etc/docker-gen/templates volume
cd $(docker inspect nginx-gen-data  | grep '"/etc/docker-gen/templates": "' | cut -f4 -d'"')
# download nginx docker-gen template
curl -o nginx.tmpl https://raw.githubusercontent.com/jwilder/docker-gen/master/templates/nginx.tmpl
```

### Create nginx reverse proxy 
This nginx takes config from ndinx-data container
```
docker run -d -p 80:80 --name nginx --volumes-from nginx-data -t nginx
```

### Start docker-gen
This container uses template from nginx-gen-data volume and generates config to nginx-data volume (which is ultimately used by nging reverse proxy)
```
docker run -d --name nginx-gen --volumes-from nginx-data --volumes-from nginx-gen-data -v /var/run/docker.sock:/var/run/docker.sock -t emsi/docker-gen -notify-sighup nginx -watch /etc/docker-gen/templates/nginx.tmpl /etc/nginx/conf.d/default.conf
```
