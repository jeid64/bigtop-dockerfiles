FROM    fedora:20

# Update packages first
RUN yum update -y

# set a cheap, simple password for great convenience
RUN echo 'root:bigtop' | chpasswd

# Enable Puppet repo
RUN rpm -ivh http://yum.puppetlabs.com/puppetlabs-release-fedora-20.noarch.rpm 

# Get BigTop packaging code
RUN yum install -y git puppet 
RUN git clone https://github.com/jeid64/bigtop.git
RUN cd /bigtop/; git checkout bigtop-1359-patch

# Copy in JDK6 and 7 into the container to be installed by Puppet. Note: You must provide these, we don't.
COPY jdk/jdk-6u45-linux-amd64.rpm /bigtop/bigtop_toolchain/files/
COPY jdk/jdk-7u60-linux-x64.gz /bigtop/bigtop_toolchain/files/

RUN puppet apply --modulepath=/bigtop -e "include bigtop_toolchain::installer"
