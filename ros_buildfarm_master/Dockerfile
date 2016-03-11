FROM ros_buildfarm_base:latest
# Could just run the install script directly
# https://github.com/ros-infrastructure/buildfarm_deployment_config/blob/master/install_prerequisites.bash
RUN cd /root && git clone https://github.com/lucasw/buildfarm_deployment_config.git && \
    cd buildfarm_deployment_config && ./reconfigure.bash master
# TODO something in creates the ability to install jenkins but fails
# would like to stop at that point
RUN cd /root/buildfarm_deployment_config && ./reconfigure_finish.bash master
# install manually
RUN apt-get install -y jenkins
# need to rerun this also - but it doesn't work, these have to be run in the
# live container in order to bring up the server.
# Maybe there is a simple command to do that, and ought to be a CMD ?
#RUN cd /root/buildfarm_deployment_config && ./reconfigure.bash master
#RUN cd /root/buildfarm_deployment_config && ./reconfigure_finish.bash master
# TODO is the exposed too late?  Does order matter here?
EXPOSE 80
EXPOSE 443
