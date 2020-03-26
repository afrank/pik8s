# pik8s
Until recently running kubernetes on a raspberry pi was kinda not awesome, mostly because it wasn't powerful enough to handle the overhead of k8s with enough beef leftover to do anything useful. With the pi4 you get enough power from the quad-A72 to handle k8s with enough leftover to be interesting. Also not using asymetric cores like other ARM-based SBCs makes handling containerized applications a little more straightforward. pik8s aims to make kubernetes on raspberry pi a little easier by:
- Adding the necessary repos/packages to a lightweight rasbian foundation
- Setting up the local environment to properly run Kubernetes
- Setting up kubernetes automatically with the correct settings for running on the pi as a one-node cluster
- Adding software within kubernetes to make it easier to use, and easier to add useful software to
- Providing clear instructions for usage

Don't waste your time retrofitting your mac to run a local kubernetes cluster. Just spend $35, but a pi, and install this. And you've got yourself a kubernetes cluster in your own home!

## Requirements
If I were you I wouldn't attempt to run this on anything slower than a pi4. That said, it _should_ work on anything Raspbian will work on.

## Installation
Grab the [https://downloads.raspberrypi.org/raspbian_lite_latest](latest Rasbian Lite image), unzip it, and flash it onto an SD card. 

Once you boot from that, log in as username *pi* password *raspberry*, and run `sudo su -`.

After you've run sudo su to gain root privileges, run
```
curl -s https://rpik8s.com/install > install
chmod +x install
./install
```
