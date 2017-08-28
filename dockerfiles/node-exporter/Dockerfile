######################## Dockerfile for cAdvisor 0.26.1 #################################
#
# This Dockerfile builds a basic installation of cAdvisor.
#
# cAdvisor (Container Advisor) provides container users an understanding of the resource usage
# and performance characteristics of their running containers. It is a running daemon that collects,
# aggregates, processes, and exports information about running containers.
#
# To build this image, from the directory containing this Dockerfile
# (assuming that the file is named Dockerfile):
# docker build -t <image_name> .
#
# To Start cAdvisor server create a container from the image created from Dockerfile and
# link 8080 to the port.
# docker run --name <container_name> -p <port_number>:8080 -d <image_name>
#
# Access cAdvisor web user interface from browser
# http://<hostname>:<port_number>
#
##########################################################################################

# Base image
FROM s390x/ubuntu:16.04

# The author
MAINTAINER LoZ Open Source Ecosystem (https://www.ibm.com/developerworks/community/groups/community/lozopensource)

ENV SOURCE_DIR=/tmp/source
WORKDIR $SOURCE_DIR

# Set Environmental Variables
ENV GOPATH=$SOURCE_DIR
ENV PATH=/opt/gccgo/bin:$PATH:$GOPATH/bin

RUN apt-get update && apt-get install -y wget

# Install Go v1.8.x
RUN wget https://storage.googleapis.com/golang/go1.8.1.linux-s390x.tar.gz \
&& tar -xvf go1.8.1.linux-s390x.tar.gz \
&& mv go /usr/local

ENV PATH=$PATH:/usr/local/go/bin

# Install dependencies
RUN apt-get update && apt-get install -y \
    git build-essential \
	
# Install godep tool
 && go get github.com/tools/godep \

# Create directory and Change the work directory
 && mkdir -p $GOPATH/src/github.com/prometheus && cd $GOPATH/src/github.com/prometheus \

# Checkout the code from repository
# && git clone https://github.com/google/cadvisor.git -b v0.26.1 \
 && git clone https://github.com/prometheus/node_exporter.git \
 && cd node_exporter \

# Build node-exporter
 && make build \
 && cp ./node_exporter /usr/bin \
 && apt-get remove -y git && apt-get autoremove -y && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Port for cAdvisor
EXPOSE 9100
USER nobody

# Command to execute
CMD ["/usr/bin/node_exporter"]

# End of Dockerfile
