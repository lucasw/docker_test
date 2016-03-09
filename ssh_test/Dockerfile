FROM ubuntu:14.04
# Could just run the install script directly
# https://github.com/ros-infrastructure/buildfarm_deployment_config/blob/master/install_prerequisites.bash
RUN apt-get update && apt-get -y install openssh-server
RUN mkdir /var/run/sshd
RUN useradd -ms /bin/bash test -p test
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
