
[![Build
Status](https://travis-ci.org/mkilivan/image-builder-raspberrypi.svg?branch=master)](https://travis-ci.org/mkilivan/image-builder-raspberrypi)
[![](https://images.microbadger.com/badges/image/mkilivan/image-builder-raspberrypi.svg)](https://microbadger.com/images/mkilivan/image-builder-raspberrypi
"Get your own image badge on microbadger.com")
[![](https://images.microbadger.com/badges/version/mkilivan/image-builder-raspberrypi.svg)](https://microbadger.com/images/mkilivan/image-builder-raspberrypi
"Get your own version badge on microbadger.com")

### Yocto Image Builder
This version of yocto is also available on [DockerHub](https://hub.docker.com/r/mkilivan/image-builder-raspberrypi). You can pull the docker image from DockerHub using the command:
```
docker pull mkilivan/image-builder-raspberrypi
```
To run the docker image without doing a git pull, use the following:
```
docker run -ti --volume=${PWD}/project:/home/yocto/project mkilivan/image-builder-raspberrypi
```
#### Possible Issues
Depending on the host system being run, when executing yocto builds, one might run into the fullowing error:
```
ERROR: No space left on device or exceeds fs.inotify.max_user_watches?
```
Please check the max_user_watches value on your host system using:
```
cat /proc/sys/fs/inotify/max_user_watches
```
If the value is set too low (eg: 8192), use the following command to set it properly. Please ensure that you have root/sudo priviledges to do this.
```
sudo sysctl fs.inotify.max_user_watches=1000000
```
This should solve the problem with No space left on device errors.
### Local Build
```
./local_build.sh
```
This script build the image on your Linux PC.
### Step-by-Step Build
```
mkdir ~/bin
PATH=~/bin:$PATH
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```
#### Download the Source
```
mkdir WORKING_DIRECTORY
cd WORKING_DIRECTORY
repo init -u https://github.com/mkilivan/image-builder-raspberrypi.git
repo sync
```
#### Configure
```
source oe-init-build-env
```
Edit conf/bblayers.conf file. It should look like (Note: change absolute path to your home directory)
```
BBLAYERS ?= " \
  /path/to/poky/meta \
  /path/to/poky/meta-poky \
  /path/to/poky/meta-openembedded/meta-oe \
  /path/to/poky/meta-openembedded/meta-multimedia \
  /path/to/poky/meta-openembedded/meta-networking \
  /path/to/poky/meta-openembedded/meta-python \
  /path/to/poky/meta-openembedded/meta-filesystems \
  /path/to/poky/meta-virtualization \
  /path/to/poky/meta-raspberrypi \
  "
```
#### Build
```
bitbake core-image-base
````
### Flash the image to SD card
Assuming an SD card is attached as /dev/sdd, you can write the image to the card as follows:
```
sudo dd if=./tmp/deploy/images/raspberrypi3/chaos-image-base-raspberrypi3.rpi-sdimg of=/dev/sdd
```
