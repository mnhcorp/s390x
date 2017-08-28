######################## Dockerfile for cAdvisor 0.26.1 #################################
#
# This Dockerfile builds a basic installation of Grafana.
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
RUN wget https://storage.googleapis.com/golang/go1.8.1.linux-s390x.tar.gz \
&& tar -xvf go1.8.1.linux-s390x.tar.gz \
&& mv go /usr/local

ENV PATH=$PATH:/usr/local/go/bin

# Install dependencies
RUN apt-get update && apt-get install -y \
    git build-essential curl
RUN apt-get install -y g++ flex bison gperf ruby perl \
  libsqlite3-dev libfontconfig1-dev libicu-dev libfreetype6 libssl-dev \
  libpng-dev libjpeg-dev python libx11-dev libxext-dev

RUN curl -sL https://deb.nodesource.com/setup_8.x | bash - 
RUN apt-get install -y nodejs npm \

 && ln -s /usr/bin/nodejs /usr/bin/node \

# Install PhantomJS
&& mkdir -p $GOPATH/src/github.com/phantomjs \
&& cd $GOPATH/src/github.com/phantomjs \
&& git clone git://github.com/ariya/phantomjs.git \
&& cd phantomjs \
&& git checkout 2.1.1 \
&& git submodule init \
&& git submodule update \
&& python build.py \
&& cp bin/phantomjs /usr/bin \
	
# Install godep tool
 && go get github.com/tools/godep \

# Create directory and Change the work directory
 && mkdir -p $GOPATH/src/github.com/grafana \
 && cd $GOPATH/src/github.com/grafana \

# Checkout the code from repository
 && git clone https://github.com/grafana/grafana \
 && cd grafana \

# Build grafana
 && cd $GOPATH/src/github.com/grafana/grafana \
 && go run build.go setup \
 && go run build.go build \
 && npm install -g yarn \
 && yarn install --pure-lockfile --ignore-engines\
 && npm run build \
 && cp $GOPATH/src/github.com/grafana/grafana/bin/grafana-server /usr/bin \
 && apt-get remove -y git && apt-get autoremove -y && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Port for cAdvisor
EXPOSE 3000

# Command to execute
CMD ["grafana-server"]

# End of Dockerfile
