FROM ros:noetic-robot-focal
ARG OAK_WS=/root/oak_ffc_ws/
ARG DEPTH_CORE=/root/3rd_party/
ARG ROS_VERSION=noetic
ARG DEPTH_AI_CORE=2.26.0

SHELL ["/bin/bash", "-c"] 

WORKDIR ${DEPTH_CORE}

RUN apt-get update &&\
    apt-get install -y \
    vim wget pip clang-format gdb xterm\
    ros-noetic-depthai-bridge \
    ros-noetic-depthai-ros-msgs \
    ros-noetic-image-transport \
    ros-noetic-cv-bridge \
    python3-catkin-tools


# install depth-ai core   
RUN wget https://github.com/luxonis/depthai-core/releases/download/v2.26.0/depthai-core-v${DEPTH_AI_CORE}.tar.gz  &&\
    tar -xvf depthai-core-v${DEPTH_AI_CORE}.tar.gz &&\
    rm depthai-core-v${DEPTH_AI_CORE}.tar.gz &&\
    cd depthai-core-v${DEPTH_AI_CORE} &&\
    mkdir build &&\
    cd build &&\
    cmake .. &&\
    make -j$(nproc) &&\
    make install

RUN cd ${DEPTH_CORE}/depthai-core-v${DEPTH_AI_CORE}/build/install/include &&\
    mv ./depthai /usr/local/include/depthai &&\
    mv ./depthai-shared /usr/local/include/depthai-shared &&\
    mv ./depthai-bootloader-shared /usr/local/include/depthai-bootloader-shared

# install spdlog
RUN wget https://github.com/gabime/spdlog/archive/refs/tags/v1.8.2.tar.gz &&\
    tar -xvf v1.8.2.tar.gz &&\
    rm v1.8.2.tar.gz &&\
    cd spdlog-1.8.2 &&\
    mkdir build &&\
    cd build &&\
    cmake .. &&\
    make -j$(nproc) &&\
    make install

# install fmt
RUN wget https://github.com/fmtlib/fmt/archive/refs/tags/7.1.3.tar.gz &&\
    tar -xvf 7.1.3.tar.gz &&\
    rm 7.1.3.tar.gz &&\
    cd fmt-7.1.3 &&\
    mkdir build &&\
    cd build &&\
    cmake .. &&\
    make -j$(nproc) &&\
    make install

# ROS ENV setup
RUN echo "source /opt/ros/${ROS_VERSION}/setup.bash" >> ~/.bashrc &&\
    source ~/.bashrc
    


# Build oak_ffc_4p_ros
COPY . ${OAK_WS}/src/oak_ffc_4p_ros

RUN source "/opt/ros/${ROS_VERSION}/setup.bash" &&\
    cd ${OAK_WS} &&\
    catkin build &&\
    echo "source ${OAK_WS}/devel/setup.bash" >> ~/.bashrc &&\
    source ~/.bashrc

WORKDIR ${OAK_WS}


# RUN source "/opt/ros/noetic/setup.bash" &&\
#     cd ${OAK_WS} &&\
#     catkin_make
