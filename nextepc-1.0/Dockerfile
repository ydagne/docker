# Docker image for NextEPC virtual core network
#
# Examples to build and deploy the EPC core
#
#     Build command:
#          # docker build --force-rm -t nextepc:1.0 .
#
#     Running a container:
#
#          # docker run --rm -it --cap-add=NET_ADMIN --sysctl net.ipv6.conf.all.disable_ipv6=0 \
#            --name epc1 -p 8000:3000 --device /dev/net/tun nextepc:1.0
#
#     Notes:
#       - The container requires NET_ADMIN permission in order to create a tun interface
#       - The defualt command automatically configures the EPC with the necessary services,
#         and runs a webserver for HSS management.
#       - Support for ipv6 was not added
#

FROM ubuntu:xenial

# Install updates
RUN apt-get -y update

# Install dependencies
RUN apt-get -y install autoconf libtool gcc pkg-config git flex bison mongodb libsctp-dev libgnutls28-dev libgcrypt-dev libssl-dev libmongoc-dev libbson-dev libyaml-dev

# Download the source code for nextepc
RUN git clone https://github.com/acetcom/nextepc

# Build and install
RUN cd nextepc/ && autoreconf -iv && ./configure --prefix=`pwd`/install && make -j `nproc` && make install

# Create directory for MongoDB database and log
RUN mkdir -p /var/lib/mongo && mkdir -p var/log/mongo

# Configuration files are located in etc/nextepc/*.conf
# NextEPC provides an alternative management interface via Web User Interface

# Install Node.js webserver
RUN apt-get -y install curl gnupg
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash -
RUN apt-get -y install nodejs
# Build the web application
RUN cd nextepc/webui && npm install && npm run build
# Expose webservers listenning port
EXPOSE 3000

WORKDIR nextepc


CMD \
# Start the database server
    mongod --smallfiles --fork --dbpath /var/lib/mongodb --logpath /var/log/mongodb/mongodb.log \
# Create a tun interface to be used by the P-GW
 && ip tuntap add name pgwtun mode tun \
 && ip addr add 45.45.0.1/16 dev pgwtun \
 && ip addr add cafe::1/16 dev pgwtun \
 && ip link set pgwtun up \
# Start the web server
 && { npm run start --prefix webui & \
# Start the EPC deamon
 ./nextepc-epcd; }

# ---------------Note-----------------------------------------------------
# By default, an LTE UE will receive a IP address with the network address
# of 45.45.0.0/16 or cafe::0/64. To make UEs reachable from outside, add
# a route of both 45.45.0.0/16 and cafe::0/64 to go the PGW IP address
#  # ip route add 45.45.0.0/16 via <P-GW ADDRESS>
#  # ip route add cafe::0/64 via  <P-GW ADDRESS>

