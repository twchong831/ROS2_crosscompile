# ROS2_crosscompile

set ros2 cross compile ENV.

## MACOS

- M2 macbook Air

### multipass

#### Install

```bash
brew install --cask multipass
```

#### Instance

```bash
# find multipass images
multipass find
# Image                       Aliases           Version          Description
# 20.04                       focal             20240513         Ubuntu 20.04 LTS
# 22.04                       jammy             20240514         Ubuntu 22.04 LTS
# 23.10                       mantic            20240514         Ubuntu 23.10
# 24.04                       noble,lts         20240523.1       Ubuntu 24.04 LTS

# generate instance & run
multipass launch ros2-humble --name u-ros2 -c 4 -m 8G -d 100G -v
# ros2-humble : using image name
# name : instance name
# -c : num. of CPU
# -m : memory
# -d : disk storage
```

#### run

```bash
multipass shell u-ros2
```

#### shared DIR

```bash
multipass mount [shared_dir] [name]:/home/ubuntu/share
```

#### else

```bash
# information
multipass info [name]

# instance list
multipass list
multipass ls

# delete
multipass umount [name]
multipass stop [name]
multipass delete [name]
multipass purge [name]
```

#### ERROR

- multipass instance가 동작하지 제대로 동작하지 않는 경우
- 최근 업데이트 이후 mac 자체 방화벽에 의해 동작하지 않는 경우가 발생
- [설정] - [네트워크] - [방화벽]을 off해주면 동작함을 확인

### docker

- cross compile 앱을 구동할 때 docker가 필요로 함
- multipass instance 내 docker 구동 환경 구축 필요

```bash
sudo apt-get install docker
```

#### ERROR

- docker ps -a 실행 시, Permission Error 발생

```bash
sudo chmod 666 /var/run/docker.sock
```

#### delete all container

```bash
docker rm $(docker ps --filter 'status=exited' -a -q)
```

### ROS2 Cross Compile Tool

[Link](https://github.com/ros-tooling/cross_compile)

- Architecture : armhf, aarch64, x86_64
- ROS Distro
  - ROS : melodic, noetic
  - ROS2 : foxy, galactic, humble, rolling
- OS : Ubuntu, Debian

#### Install

```bash
sudo apt-get install qemu-user-static
sudo apt-get install python3
sudo apt-get install python3-pip

# install ros_cross_compile
pip3 install ros_cross_compile
```

-------

#### ERROR

- pip3로 설치했음에도
- ros_cross_compile 명령어가 없는 경우

```bash
sudo find / -name ros_cross_compile
# /home/ubuntu/.local/bin/ros_cross_compile
# /home/ubuntu/.local/lib/python3.10/site-packages/ros_cross_compile

sudo cp ~/.local/bin/ros_cross_compile /usr/bin
```

- exec /bin/bash: exec format error
  - windows 11 wsl2 ubuntu22.04에서 발생
  - 아키텍처 차이에 따른 오류...
  - 생성된 docker 컨테이너를 모두 제거한 후 재실행하니 생성됨을 확인
  - 이후 재 컴파일 시에 다시 발생...

-------

#### build

- PCL 기반 point cloud를 생성하고 특정 시간마다 point cloud를 회전하여 이를 publish하는 node

```bash
# Project 구조
project_DIR
├── build.sh
└── src
    └── hlkros
        ├── CMakeLists.txt
        ├── include
        │   ├── hlkros
        │   │   └── common.h
        │   ├── rosnode.h
        │   └── transform.h
        ├── package.xml
        └── src
            ├── main.cpp
            ├── rosnode
            │   ├── CMakeLists.txt
            │   └── rosnode.cpp
            └── transform
                ├── CMakeLists.txt
                └── transform.cpp
```

- 작성한 코드를 바탕으로 크로스 컴파일 수행

```bash
ros_cross_compile . --arch aarch64 --os ubuntu --rosdistro foxy
```
