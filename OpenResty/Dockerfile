# © Copyright IBM Corporation 2017, 2019.
# LICENSE: Apache License, Version 2.0 (http://www.apache.org/licenses/LICENSE-2.0)

########## Dockerfile for OpenResty version 1.15.8.2 #########
#
# This Dockerfile builds a basic installation of OpenResty.
#
# OpenResty is a full-fledged web platform that integrates the standard Nginx core, LuaJIT, 
# many carefully written Lua libraries, lots of high quality 3rd-party Nginx modules, and most of their external dependencies. 
# It is designed to help developers easily build scalable web applications, web services, and dynamic web gateways.
#
# To build this image, from the directory containing this Dockerfile
# (assuming that the file is named Dockerfile):
# docker build -t <image_name> .
#
# To start OpenResty run the below command:
# docker run -d --name <container_name> -p <host_port>:80 <image>
#
# Provide custom configuration file
# docker run -d --name <container_name> -v <path_on_host>:/usr/local/openresty/nginx/conf/nginx.conf -p <host_port>:80 <image>
# Reference :
# https://openresty.org/
#
##################################################################################

# Base Image
FROM s390x/ubuntu:16.04

# The author
LABEL maintainer="LoZ Open Source Ecosystem (https://www.ibm.com/developerworks/community/groups/community/lozopensource)"

ARG RESTY_VERSION="1.15.8.2"
ARG NGINX_VERSION="1.15.8"
ARG RESTY_J="1"
ARG RESTY_CONFIG_OPTIONS="\
	--without-http_redis2_module \
	--with-http_iconv_module \
	--with-http_postgres_module \
"

ENV SOURCE_ROOT=/tmp/source PATH=$PATH:/usr/local/openresty/bin

WORKDIR $SOURCE_ROOT

# Install dependencies
RUN	DEBIAN_FRONTEND=noninteractive apt-get update \
	&& DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
	    ca-certificates \
		dos2unix \
		gcc \
		git \
		hgview \
		libcurl4-openssl-dev \
		libncursesada*-dev \
		libpcre3 \
		libpcre3-dev \
		libpq-dev \
		libreadline-dev \
		libssl-dev \
		make \
		openssl \
		patch \
		perl \
		postgresql \
		tar \
		wget \
	&& ln -s make /usr/bin/gmake \
# Download the source code
	&& cd $SOURCE_ROOT \
	&& wget https://openresty.org/download/openresty-${RESTY_VERSION}.tar.gz \
	&& tar -xvf openresty-${RESTY_VERSION}.tar.gz \
# Comment out the below lines in file /<source_root>/openresty-1.15.8.2/configure
	&& cd openresty-${RESTY_VERSION} \
	&& sed -i '730,773 s/^/#/' configure \
	&& sed -i '723 s/^/#/' configure \
	&& sed -i '704,713 s/^/#/' configure \
# Build and install OpenResty
	&& cd $SOURCE_ROOT \
	&& rm -rf $SOURCE_ROOT/openresty-${RESTY_VERSION}/bundle/LuaJIT-2.1-20190507/* \
	&& wget https://github.com/openresty/luajit2/archive/v2.1-20190912.tar.gz \
	&& tar -zxvf v2.1-20190912.tar.gz \
	&& cp -r $SOURCE_ROOT/luajit2-2.1-20190912/* $SOURCE_ROOT/openresty-${RESTY_VERSION}/bundle/LuaJIT-2.1-20190507/ \
	&& cd $SOURCE_ROOT/openresty-${RESTY_VERSION} \
	&& ./configure $RESTY_CONFIG_OPTIONS \
	&& make -j2 \
	&& make install \
# Configure Nginx module
    && cd $SOURCE_ROOT/openresty-${RESTY_VERSION}/build/nginx-${NGINX_VERSION} \
    && ./configure && make && make install \
# Clean up cache data and remove dependencies which are not required
	&&	apt-get -y remove \
		dos2unix \
		gcc \
		git \
		hgview \
		make \
		openssl \
		patch \
		postgresql \
		wget \
	&&	apt-get autoremove -y \
	&& 	apt autoremove -y \
	&& 	rm -rf $SOURCE_ROOT/* \
	&& 	apt-get clean \
	&& 	rm -rf /var/lib/apt/lists/*
	
EXPOSE 80
	
CMD ["openresty", "-g", "daemon off;"]
	
# End of Dockerfile
