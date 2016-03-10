FROM ubuntu:14.04
# Could just run the install script directly
# https://github.com/ros-infrastructure/buildfarm_deployment_config/blob/master/install_prerequisites.bash
RUN apt-get update && apt-get -y install openssh-server
RUN mkdir /var/run/sshd
# -p password doesn't seem to work
# RUN useradd -ms /bin/bash -p newuser newuser
RUN useradd -ms /bin/bash newuser
RUN echo 'newuser:newuser' | chpasswd
RUN adduser newuser sudo
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
