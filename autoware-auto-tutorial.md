## Installation

- Install ADE from here https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/installation.html
- Setup ADE and project checkout

```console
$ mkdir ~/adehome
$ cd ~/adehome
$ touch .adehome
```
- Checkout the project

```console
$ cd ~/adehome
$ git clone https://gitlab.com/autowarefoundation/autoware.auto/AutowareAuto.git
```

## How to build

```console
$ ade start --update
$ ade enter
$ cd AutowareAuto
ade$ vcs import < autoware.auto.$ROS_DISTRO.repos 
ade$ colcon build
ade$ colcon test
ade$ colcon test-result
```
