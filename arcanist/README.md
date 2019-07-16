arcanist container build on top of dkdde/arcanist and php containers

### Create data only container:

`docker run -v /arc --name arcanist-data dkdde/arcanist:latest`

### Run bind container:

`docker run --volumes-from bind9-data --name bind9 -p 53:53 -p 53:53/udp -d emsi/bind9`

`docer run --rm -ti --volumes-from arcanist -v /path_to/custom.pem:/arc/libphutil/resources/ssl/custom.pem -v /path_to_your/.arcrc:/root/.arcrc -v $(pwd):/work -w /work emsi/arcanist diff`
