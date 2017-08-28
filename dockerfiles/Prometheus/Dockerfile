######################## Dockerfile for Prometheus #################################
#
# This Dockerfile builds an installation of Prometheus.
#
# Prometheus, a Cloud Native Computing Foundation project, is a systems and service monitoring system. 
# It collects metrics from configured targets at given intervals, evaluates rule expressions, displays the results, 
# and can trigger alerts if some condition is observed to be true.
#
# To build this image, from the directory containing this Dockerfile
# (assuming that the file is named Dockerfile):
# docker build -t <image_name> .
#
# To Start Prometheus server create a container from the image created from Dockerfile and
# link 9090 to the port.
# docker run --name <container_name> -p <port_number>:9090 -d <image_name>
#
# Access Prometheus web user interface from browser
# http://<hostname>:<port_number>
#
##########################################################################################

# Base image
FROM s390x/ubuntu:16.04

# The author
MAINTAINER nasir.hossain@fatbrain.io

ENV SOURCE_DIR=/tmp/source
WORKDIR $SOURCE_DIR

# Set Environmental Variables
ENV GOPATH=$SOURCE_DIR
ENV PATH=/opt/gccgo/bin:$PATH:$GOPATH/bin

RUN apt-get update && apt-get install -y wget
RUN wget https://storage.googleapis.com/golang/go1.8.1.linux-s390x.tar.gz \
&& tar -xvf go1.8.1.linux-s390x.tar.gz \
&& mv go /usr/local

ENV PATH=$PATH:/usr/local/go/bin

# Install dependencies
RUN apt-get update && apt-get install -y \
    git build-essential \ 
	libseccomp-dev \
	
# Install godep tool
 && go get github.com/tools/godep \

# Create directory and Change the work directory
 && mkdir -p $GOPATH/src/github.com/prometheus && cd $GOPATH/src/github.com/prometheus \

# Checkout the code from repository
 && git clone https://github.com/prometheus/prometheus.git \
 && cd prometheus \

# Build prometheus
 && cd $GOPATH/src/github.com/prometheus/prometheus \
 && make build \
 && cp $GOPATH/src/github.com/prometheus/prometheus/prometheus /usr/bin \
 && cp promtool /usr/bin \
 && mkdir -p /etc/prometheus \
 && cp documentation/examples/prometheus.yml /etc/prometheus/prometheus.yml \
 && mkdir -p /usr/share/prometheus \
 && cp -avp console_libraries/ /usr/share/prometheus/ \
 && cp -avp consoles/ /usr/share/prometheus/consoles/ \
 && apt-get remove -y git && apt-get autoremove -y && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Port for cAdvisor
EXPOSE 9090

# Command to execute
RUN mkdir /prometheus
VOLUME [ "/prometheus" ]
WORKDIR    /prometheus
ENTRYPOINT [ "/usr/bin/prometheus" ]
CMD        [ "-config.file=/etc/prometheus/prometheus.yml", \
             "-storage.local.path=/prometheus", \
             "-web.console.libraries=/usr/share/prometheus/console_libraries", \
             "-web.console.templates=/usr/share/prometheus/consoles" ]

# End of Dockerfile
