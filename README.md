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

Build all the following images:

  git clone https://github.com/lucasw/docker_test.git
  cd docker_test
  docker build -t ssh_server ssh_server
  docker build -t ros_buildfarm_base ros_buildfarm_base
  docker build -t ros_buildfarm_master ros_buildfarm_master
  docker build -t ros_buildfarm_repo ros_buildfarm_repo
  docker build -t ros_buildfarm_slave ros_buildfarm_slave

It should be fine to run the docker master/repo/slave builds in parallel in different terminals.

(TODO does docker rebuild parents automatically if children are rebuilt?)

Also rerun the docker build instructions when the Dockerfiles change.

## Network Setup

Add to /etc/hosts:

    172.18.0.30 custom-master
    172.18.0.31 custom-repo
    172.18.0.32 custom-slave

The 'Running puppet' stage at the end may take a long time- 10 minutes on my laptop.

### Launch containers

Network setup::

    docker network create --subnet=172.18.0.0/16 bfnet
    docker run --net bfnet --ip 172.18.0.30 --hostname custom-master --add-host custom-repo:172.18.0.31 --add-host custom-slave:172.18.0.32 -d -P --name master0 ros_buildfarm_master
    docker run --net bfnet --ip 172.18.0.31 --hostname custom-repo --add-host custom-master:172.18.0.30 --add-host custom-slave:172.18.0.32 -d -P --name repo0 ros_buildfarm_repo
    docker run --net bfnet --ip 172.18.0.32 --hostname custom-slave --add-host custom-master:172.18.0.30 --add-host custom-repo:172.18.0.31 -d -P --name slave0 ros_buildfarm_slave


TODO how to make sure keyscan -H is consistent?
Need to copy in ssh files?

TODO set mac addresses to be consistent with --mac-address

### Get processes running inside containers

For reasons that need to be discovered it is necessary to rerun the ./reconfigure.bash steps in a live container- these steps were run initially when the Dockerfiles were processed, but perhaps processes that are launched by them are not automatically relaunced when the container is set up.
TODO Need to fix Dockerfiles so this isn't necessary.

Ssh into each container:

    ssh newuser@custom-master
    # password is also `newuser`
    sudo su
    cd /root/buildfarm_deployment_config
    ./reconfigure.bash master
    ./reconfigure_finish.bash master
    exit
    exit

    ssh newuser@custom-repo
    # password is also `newuser`
    sudo su
    cd /root/buildfarm_deployment_config
    ./reconfigure.bash repo
    ./reconfigure_finish.bash repo
    exit
    exit

    ssh newuser@custom-slave
    # password is also `newuser`
    sudo su
    cd /root/buildfarm_deployment_config
    ./reconfigure.bash slave
    ./reconfigure_finish.bash slave
    exit
    exit

'Running puppet' should be much quicker now, around a minute.

So the above steps will have an effect as long as the container exists- if it is removed then that

## Results

http://custom-master should show a running jenkins instance inside the master container, and building_repository should be visible as an executor.
The slave should also be visible, but last I tried it did not show up- TODO debug that, probably something misconfigured about it.

## Next

generate_all_jobs.py from the host (or any computer that can talk to custom-master).
It failed when trying to run it in the virtual environment in custom-repo.
TODO document that failure.

## Debug

Look at /var/log/puppet.log when things are not operating correctly.
The jenkins log and apache log also may be useful.

## Misc

Development log for this is here: http://lucasw.github.io/docker/
It can't be followed directly but useful commands and examples of command output are there.
