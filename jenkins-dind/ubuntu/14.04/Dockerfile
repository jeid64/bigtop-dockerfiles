FROM ubuntu:14.04

# Tell Apt that no one will be able to answer apt dialogue questions and to just assume defaults.
ENV DEBIAN_FRONTEND noninteractive

# First install apt-utils so we can configure packages.
RUN apt-get update && apt-get -y install apt-utils

# Update packages
RUN apt-get upgrade -y

# generate a nice UTF-8 locale for our use
RUN locale-gen en_US.UTF-8 && update-locale LANG=en_US.UTF-8

# set a cheap, simple password for great convenience
RUN echo 'root:bigtop' | chpasswd

# Get dependencies for running Docker-in-docker
RUN apt-get install -qqy iptables ca-certificates lxc

# We need wget to fetch slave.jar and java to run it. 
RUN apt-get -y install wget curl openjdk-7-jre 

# Get the docker repo so we can get latest Docker
RUN echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list
RUN apt-get update
RUN apt-get install lxc-docker

ADD ./wrapdocker /usr/local/bin/wrapdocker
RUN chmod +x /usr/local/bin/wrapdocker

# Get additional metadata for the image
VOLUME /var/lib/docker

#Install & configure Supervisor
RUN apt-get -y install supervisor
RUN mkdir -p /var/log/supervisor
ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf

#Run Supervisor
CMD ["/usr/bin/supervisord"]
