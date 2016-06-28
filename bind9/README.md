bind9 clean docker.
All the configuration is located in /etc/named volume. It's highly recommended to use data-only container to store the configuration as described below.

### Create data only container:

`docker run --name bind9-data emsi/bind9-data`

Once run it will list the content of /etc/named volume used to store the configuration (so it's safe if you accidentaly start it again). Keep it safe as it stores the bind configuration!

### Run bind container:

`docker run --volumes-from bind9-data --name bind9 -p 53:53 -p 53:53/udp -d emsi/bind9`

### Edit the configuration
Inspect the data container to locate the volume with configuration or simply enter:

`cd $(docker inspect -f '{{(index .Mounts 0).Source }}' bind9-data)`

then you can edit the configuration and restart the bind9 container.

### Bind9 upgrade
To upgrade you can simply ```docker stop bind9; docker rm bind9``` then run the new container as shown above (`docker run --volumes-from bind9-data --name bind9 -p 53:53 -p 53:53/udp -d emsi/bind9`). All the configuration is preserved in bind9-data container.
