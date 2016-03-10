FROM ubuntu:14.04
# Could just run the install script directly
# https://github.com/ros-infrastructure/buildfarm_deployment_config/blob/master/install_prerequisites.bash
RUN apt-get update && \
  apt-get -y install \
          openssh-server \
          git \
          ruby ruby1.9.1-dev make
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
RUN gem install puppet -v 3.7 --no-rdoc --no-ri
RUN gem install librarian-puppet --no-rdoc --no-ri

RUN apt-get -y install python3-pip
RUN pip3 install -U pip
RUN librarian-puppet config rsync true --global
CMD ls -al
