FROM ubuntu:12.04

# much of this was gleaned from https://github.com/lxc/lxc/blob/lxc-0.8.0/templates/lxc-ubuntu.in
# and then heavily modified and hacked like crazy

# The base image changes policy-rc.d to stop services from being configured by apt. We change this to mirror stock Ubuntu.
RUN rm /usr/sbin/policy-rc.d; 
ADD init-fake.conf /etc/init/fake-container-events.conf

# Tell Apt that no one will be able to answer apt dialogue questions and to just assume defaults.
ENV DEBIAN_FRONTEND noninteractive

# First install apt-utils so we can configure packages.
RUN apt-get update && apt-get -y install apt-utils

# Update packages and then install SSH, DHCP, and syslog.
RUN apt-get upgrade -y && apt-get install -y ssh isc-dhcp-client rsyslog

# Get Upstart binary working. We remove a fake symlink to initctl and restore the original.
RUN rm /sbin/initctl; dpkg-divert --rename --remove /sbin/initctl

# generate a nice UTF-8 locale for our use
RUN locale-gen en_US.UTF-8 && update-locale LANG=en_US.UTF-8

# Remove some services that don't make sense in containers.
RUN /usr/sbin/update-rc.d -f ondemand remove; \
	for f in \
		/etc/init/u*.conf \
		/etc/init/mounted-dev.conf \
		/etc/init/mounted-proc.conf \
		/etc/init/mounted-run.conf \
		/etc/init/mounted-tmp.conf \
		/etc/init/mounted-var.conf \
		/etc/init/hostname.conf \
		/etc/init/tty*.conf \
		/etc/init/plymouth*.conf \
		/etc/init/hwclock*.conf \
		/etc/init/module*.conf\
	; do \
		dpkg-divert --local --rename --add "$f"; \
	done; \
	echo '# /lib/init/fstab: cleared out for bare-bones Docker' > /lib/init/fstab

# small fix for SSH in 13.10 (that's harmless everywhere else)
RUN sed -ri 's/^session\s+required\s+pam_loginuid.so$/session optional pam_loginuid.so/' /etc/pam.d/sshd

# Allow root login with passwords.
RUN sed -ri 's/^PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config

# let Upstart know it's in a container
ENV container docker

# set a cheap, simple password for great convenience
RUN echo 'root:bigtop' | chpasswd

# Fix a bug in rsyslog for 12.04 only.
RUN sed -i -e 's/^\$ModLoad imklog/#\$ModLoad imklog/g' /etc/rsyslog.conf

# Init is waiting for static-networks to come up that don't exist. Cut this out for 12.04
RUN sed -i -e 's/ and static-network-up//g' /etc/init/rc-sysinit.conf

# Allow host to talk to SSH
EXPOSE 22

# Get BigTop packaging code
RUN apt-get install -y git puppet wget sudo curl
RUN git clone https://github.com/apache/bigtop.git
RUN puppet apply --modulepath=/bigtop -e "include bigtop_toolchain::installer"

# pepare for takeoff
CMD ["/sbin/init"]
