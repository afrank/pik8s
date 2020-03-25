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
The easiest way to install this is to download the latest image and flash it onto an SD card.
### Install Docker-CE ###
```
sudo su -
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
usermod -aG docker pi
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

mkdir -p /etc/systemd/system/docker.service.d

# Restart docker.
systemctl daemon-reload
systemctl restart docker
```
### Disable swap ###
```
swapoff -a
dphys-swapfile swapoff
```
###Set up iptables
```
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system

# ensure legacy binaries are installed
sudo apt-get install -y iptables arptables ebtables

# switch to legacy versions
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
sudo update-alternatives --set arptables /usr/sbin/arptables-legacy
sudo update-alternatives --set ebtables /usr/sbin/ebtables-legacy
```
###Install k8s packages
```
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
```
###Bootstrap the cluster
```
kubeadm init --pod-network-cidr=10.244.0.0/16

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

mkdir /home/pi/.kube
cp -i /etc/kubernetes/admin.conf /home/pi/.kube/config
chown -R pi. /home/pi/.kube

# untaint the master
kubectl taint nodes --all node-role.kubernetes.io/master-

# Set up flannel
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml
```

###Next steps
* Setup Helm
* Set up ingress
