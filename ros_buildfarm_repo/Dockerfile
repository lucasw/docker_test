FROM ros_buildfarm_base:latest
# Could just run the install script directly
# https://github.com/ros-infrastructure/buildfarm_deployment_config/blob/master/install_prerequisites.bash
# for venv deploy
#  TODO apt-get update && sudo apt-get install python3 python3-all python3-pip
RUN cd /root && git clone https://github.com/lucasw/buildfarm_deployment_config.git && \
    cd buildfarm_deployment_config && ./reconfigure.bash repo
RUN cd /root/buildfarm_deployment_config && ./reconfigure_finish.bash repo
# TODO need to log in and rerun reconfigure.bash?
EXPOSE 80
EXPOSE 443
