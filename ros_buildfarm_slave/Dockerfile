FROM ros_buildfarm_base:latest
# Could just run the install script directly
# https://github.com/ros-infrastructure/buildfarm_deployment_config/blob/master/install_prerequisites.bash
RUN cd /root && git clone https://github.com/lucasw/buildfarm_deployment_config.git && \
    cd buildfarm_deployment_config && ./reconfigure.bash slave
# RUN cd /root/buildfarm_deployment_config && ./reconfigure_finish.bash slave
