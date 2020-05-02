# pik8s
## A Kubernetes Installer for your Raspberry Pi!
Until recently running kubernetes on a raspberry pi was kinda not awesome, mostly because it wasn't powerful enough to handle the overhead of k8s with enough beef leftover to do anything useful. With the pi4 you get enough power from the quad-A72 to handle k8s with enough leftover to be interesting. Also not using asymetric cores like other ARM-based SBCs makes handling containerized applications a little more straightforward. Using the pik8s installer in combination with a vanilla raspbian image will take care of various things for you, like:
- Adding the necessary repos/packages to a lightweight rasbian foundation
- Setting up the local environment to properly run Kubernetes
- Setting up kubernetes automatically with the correct settings for running on the pi as a one-node cluster
- Adding software within kubernetes to make it easier to use, and easier to add useful software to
- Providing clear instructions for usage

Don't waste your time retrofitting your mac to run a local kubernetes cluster. Just spend $35, buy a pi, and install this. And you've got yourself a kubernetes cluster in your own home!

## Requirements
Kubernetes doesn't run great on anything slower than the pi4, rk3399, or equivilent. That said this software _should_ work on anything Raspbian supports.

## Installation
Grab the [latest Raspbian Lite Image](https://downloads.raspberrypi.org/raspbian_lite_latest), unzip it, and flash it onto an SD card. 

Once you boot from that, log in as username *pi* password *raspberry*, and run `sudo su -`.

After you've run sudo su to gain root privileges, run
```
curl -s https://pik8s.com/install > install
chmod +x install
./install
```
Alternatively, if you trust me, you can run
```
curl -s https://pik8s.com/install | sudo bash -x
```
