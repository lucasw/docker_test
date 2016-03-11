# docker_test

Run three docker containers that will run a minimal example of the ros buildfarm:

    custom-master - this runs jenkins
    custom-repo - this runs a webserver for the packages that will be built
    custom-slave - this executes jenkins jobs

This needs to be cloned locally:

1.  https://github.com/lucasw/docker_test.git

Custom ros buildfarm repos (don't have to cloned locally unless they need to be edited, but in that cause you will need to fork them and update references to them in the scripts to the forked server location):

1.  https://github.com/lucasw/buildfarm_deployment_config.git
1.  https://github.com/lucasw/ros_buildfarm_config.git
1.  https://github.com/lucasw/rosdistro.git

Ros buildfarm repos (don't have to clone locally, but process will make use of them read only inside the docker containers):

1.  https://github.com/ros-infrastructure/buildfarm_deployment.git
1.  https://github.com/ros-infrastructure/ros_buildfarm

## Install Docker

See official page https://docs.docker.com/engine/installation/linux/ubuntulinux/

If ubuntu official repo docker is installed already, remove because as of 14.04 it doesn't have critical features like `network`:

    sudo apt-get remove docker.io  # remove ubuntu docker
    sudo apt-get purge docker.io  # remove ubuntu docker

Then install:

    sudo apt-get install apt-transport-https ca-certificates
    sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
    sudo sh -c `echo "deb https://apt.dockerproject.org/repo ubuntu-trusty main" > /etc/apt/sources.list.d/docker.list
    sudo apt-get update
    sudo apt-get install docker-engine

## Create docker images

  git clone https://github.com/lucasw/docker_test.git
  cd docker_test
  docker build -t ssh_server ssh_server
  docker build -t ros_buildfarm_base ros_buildfarm_base
  docker build -t ros_buildfarm_master ros_buildfarm_master

It should be fine to run the docker builds in parallel in different terminals.

(or does docker rebuild parents automatically if children are rebuilt?)

Also rerun the docker build instructions when the Dockerfiles change.

## Setup

Network setup::

    docker network create --subnet=172.18.0.0/16 bfnet
    docker run --net bfnet --ip 172.18.0.30 --hostname custom-master --add-host custom-repo:172.18.0.31 --add-host custom-slave:172.18.0.32 -d -P --name master0 ros_buildfarm_master
    docker run --net bfnet --ip 172.18.0.31 --hostname custom-repo --add-host custom-master:172.18.0.30 --add-host custom-slave:172.18.0.32 -d -P --name repo0 ros_buildfarm_repo
    docker run --net bfnet --ip 172.18.0.32 --hostname custom-slave --add-host custom-master:172.18.0.30 --add-host custom-repo:172.18.0.31 -d -P --name slave0 ros_buildfarm_slave


TODO how to make sure keyscan -H is consistent?
Need to copy in ssh files?

TODO set mac addresses to be consistent with --mac-address


Development log for this is here: http://lucasw.github.io/docker/
It can't be followed directly but useful commands and examples of command output are there.
