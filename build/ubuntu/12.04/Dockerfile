FROM ubuntu:12.04

RUN rm /usr/sbin/policy-rc.d; 

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

# Get BigTop packaging code
RUN apt-get install -y git puppet wget sudo curl
RUN git clone https://github.com/apache/bigtop.git
RUN puppet apply --modulepath=/bigtop -e "include bigtop_toolchain::installer"
