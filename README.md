What is a Kubernetes cluster?

A Kubernetes cluster is a set of node machines for running containerized applications. If you’re running Kubernetes, you’re running a cluster.

At a minimum, a cluster contains a worker node and a master node. The master node is responsible for maintaining the desired state of the cluster, such as which applications are running and which container images they use. Worker nodes actually run the applications and workloads.

The cluster is the heart of Kubernetes’ key advantage: the ability to schedule and run containers across a group of machines, be they physical or virtual, on premises or in the cloud. Kubernetes containers aren’t tied to individual machines. Rather, they’re abstracted across the cluster.
How does a cluster relate to a node, a pod, and other Kubernetes terms?

Master node: The machine that controls Kubernetes nodes. This is where all task assignments originate.
Worker nodes: These machines perform the requested, assigned tasks. The Kubernetes master controls them.
Pod: A set of 1 or more containers deployed to a single node. A pod is the smallest and simplest Kubernetes object.
Service: A way to expose an application running on a set of pods as a network service. This decouples work definitions from the pods.
Volume: A directory containing data, accessible to the containers in a pod. A Kubernetes volume has the same lifetime as the pod that encloses it. A volume outlives any containers that run within the pod, and data is preserved when a container restarts.
Namespace: A virtual cluster. Namespaces allow Kubernetes to manage multiple clusters (for multiple teams or projects) within the same physical cluster.

Prerequisite for the Cluster :-

Hardware Specification —

1. 8–16 Core CPU Processor (i5 — i7 )
2. 16 Ram
3. Virtulization Enable in the BIOS for the Virtual Box .

Application Required to do setup on the laptop -

1. Oracle VM VirtualBox

for windows use this link — https://download.virtualbox.org/virtualbox/6.1.6/VirtualBox-6.1.6-137129-Win.exe

Download the above EXE and do the complete installation of the VirtualBox .

2 . Ubuntu OS

Download the VDI image of the OS from the Osboxes :-

https://sourceforge.net/projects/osboxes/files/v/vb/55-U-u/16.04/16.04.6/1604.664.7z/download

Download any flavor of the OS of 64 bit .

3. Use the License version Windows 10 64 bit laptop for the setup .


After Installtion Complete of the above 1st and 2nd Step :-

Follow below Steps :-


Create kubernetes cluster

1 . Create VMs — Master , node1 ,node2 (VDI images we downloaded make the 3 machine of this )

2. Rename the host name of them

vi /etc/hostname — rename as per machine
vi /etc/hosts — rename as per machine

3 . Create a host adapter network which use for the internal communication of the cluster
After the IP address of the machine flush

After this sometime machine not started because of host adapter — Please try to reinstall VB in admin mode .
2nd issue arise that is USB . Disable it .

4 . Assign IP address to the machine

ifconfig (adpater name — i.e. esp08c ) IP address that is your adapter network — but when u reboot the machine is will also flush .


5. Assign permanent IP address to the machine

vi /etc/network/interface

auto enp0s8
iface enp0s8 inet static
address 192.168.1.22
netmask 255.255.255.0


auto enp0s8
iface enp0s8 inet static
address 192.168.163.22
netmask 255.255.255.0

6. After this disable the swapoff

use commant

swapoff -a

and disable in

vi /ets/fstab

and last line do the comment on that line using #


7. After machine setup has been done — move towards the Kubernetes setup

8. As we know Kubernetes run on the containerized application so we need containerized platform for it . We are going with docker so need of docker installation

9. Go on the official Page of Docker and find for the setup and do the installation in my case I m using Ubuntu .

10. This all info u ll get on the Docker website also .

A . Update the apt package index and install packages to allow apt to use a repository over HTTPS:

$ sudo apt-get update

$ sudo apt-get install \
apt-transport-https \
ca-certificates \
curl \
gnupg-agent \
software-properties-common


B. Add Docker’s official GPG key:

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -


C . for the stable dockerized add the repository STABLE

sudo add-apt-repository \
“deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable”


11. Above steps are docker prequisites ….

Now Docker Installation start

12. Install Docker Engine

fisrt update the system …

$ sudo apt-get update -
$ sudo apt-get install docker-ce docker-ce-cli containerd.io

13 . After docker installation move towards kube tools installation

sudo apt-get update && sudo apt-get install -y apt-transport-https curl — install packages like curl , https


curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update

14 . Installation of tools

sudo apt-get install -y kubelet kubeadm kubectl


sudo apt-mark hold kubelet kubeadm kubectl — optional (Don’t know about the command )

14 . After installation done of the kubeadm

Don’t initialize the kubeadm instant

check for the pod network and api server

with the above two initialize the kubeadm


kubeadm init — kube intilize

— pod-network-cidr=10.244.0.0/16 — flannel

— apiserver-advertise-address=<ip-address> -api server


kubeadm init — pod-network-cidr=10.244.0.0/16 — apiserver-advertise-address=192.168.1.100 (Always run with sudo — root permission )

Adter this below stuff u need to do

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config





15 .Copy from the cmd …Please check on your command promt for below command do not copy below command — below one is for jus your explanation

kubeadm join 192.168.1.104:6443 — token s2tsj1.hqes0dm7znuy9tj9 \
— discovery-token-ca-cert-hash sha256:906bd085ce721f563358977027e02a7d3bc5a80b6d7c82f512db2da56b28662f


Paste the above command in the node1 and node2


16 . install the pod network

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml


17 .Check the all tools

kubectl get pods — all-namespaces
