## NextEPC docker project

This project is intended for deploying [NextEPC](http://nextepc.org) LTE core in a docker container.

####     Build command:
          # docker build --force-rm -t nextepc:1.0 .

####     Running a container:

          # docker run --rm -it --cap-add=NET_ADMIN --sysctl net.ipv6.conf.all.disable_ipv6=0 \
            --name epc1 -p 3000:8000 --device /dev/net/tun nextepc:1.0

####     Notes:
       - The container requires NET_ADMIN permission in order to create a tun interface
       - The defualt command automatically configures the EPC with the necessary services,
         and runs a webserver for HSS management.
       - Support for ipv6 was not added
       
## Deploying NextEPC using docker-compose

It is recommended to use docker-compose to build and deploy NextEPC. The file _docker-compose.yml_ contains
the settings that are needed for this purpose. However, feel free to modify it.

#### Build command:
        # docker-compose build --force-rm
        
#### Deploying NextEPC:
        
        # docker-compose up
        
