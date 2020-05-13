---
layout: post
title:  "Step-by-Step guide to deploy a Kubernetes Cluster on vSphere with kubeadm"
categories: "angular github"
---

I've had some frustations setting up my first Kubernetes cluster on vSphere. Hence, I decided to document a dumb step-by-step guide.

Nothing is new here. The steps I'm following are mainly from these 2 guides:

* [Installing kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/){:target="_blank"}

* [Deploying a Kubernetes Cluster on vSphere with CSI and CPI](https://cloud-provider-vsphere.sigs.k8s.io/tutorials/kubernetes-on-vsphere-with-kubeadm.html){:target="_blank"}


Let's get started! 

The kubernetes cluster I'm going to deploy is the simplest one with only **1 master node** and **1 worker node**. In addition, I have created another controller VM to run some **govc** commands. In total, I have created 3 Ubuntu VMs. They are: **k8s-cli**, **k8s-master-node1** and **k8s-worker-node1**.

All of the following commands are run in the root environment. To switch to root:

```bash
sudo su
```

### 1. Prepare 3 Ubuntu VMs

The requirements of each Ubuntu VM are:

* Ubuntu 16.04+
* 2 GB or more of RAM per machine
* 2 CPUs or more
* Full network connectivity

**1.1  Unique hostname (on all VMs)**

Change the hostname of each Ubuntu VM to a unique name. I changed the hostname to the same as the VM name, so that when I do **ssh**, I know who's who. To change the hostname of a Ubuntu machine:

```bash
vi /etc/hostname
```

Change the hostname to your-new-hostname.

```bash
vi /etc/hosts
```

Change the line of **127.0.1.1** to your-new-hostname.

If you don't want to reboot the VM, run command:

```bash
hostname your-new-hostname
```

**1.2  Install govc (on controller VM)**

Install govc on the controller VM, which is called "k8s-cli". You can find the govc download links from [https://github.com/vmware/govmomi/releases](https://github.com/vmware/govmomi/releases){:target="_blank"}.

```bash
curl -L https://github.com/vmware/govmomi/releases/download/v0.22.1/govc_linux_amd64.gz | gunzip > /usr/local/bin/govc
chmod +x /usr/local/bin/govc
```

**1.3  Enable Disk UUID of master and worker VM (on controller VM)**

Firstly, setup the vCenter connection info.

```bash
export GOVC_INSECURE=1
export GOVC_URL='https://<VC_IP>'
export GOVC_USERNAME='VC_Admin_User'
export GOVC_PASSWORD='VC_Admin_Passwd'
```

Secondly, retrieve the master and worker VM path. The following command will list all the VMs within the vCenter.

```bash
govc find / -type m
```

Finally, enable disk UUID of the master and worker VM.

```bash
govc vm.change -vm 'the path of k8s-master-node1' -e="disk.enableUUID=1"

govc vm.change -vm 'the path of k8s-worker-node1' -e="disk.enableUUID=1"
```

**1.4  Disable Swap (on master and worker VM)**

```bash
swapoff -a

vi /etc/fstab
```

Remove the any swap entry from /etc/fstab. Usually, the line looks like below:

```bash
/dev/mapper/localhost--vg-swap_1 none            swap    sw              0       0
```

**1.5  Letting iptables see bridged traffic (on master and worker VM)**

Please refer to [Letting iptables see bridged traffic](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#letting-iptables-see-bridged-traffic){:target="_blank"}.

```bash
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
```

### 2. Install Docker (on master and worker VM)

Changing the settings such that your container runtime and kubelet use systemd as the cgroup driver. Please refer to [Container runtimes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#distributed-systems){:target="_blank"}.

```bash
apt update
apt install ca-certificates software-properties-common apt-transport-https curl -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
apt update
apt install docker-ce=18.06.0~ce~3-0~ubuntu -y

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

systemctl daemon-reload
systemctl restart docker
```

### 3. Install kubeadm, kubelet and kubectl (on master and worker VM)

Actually, I didn't install kubectl on the worker node. Please refer to [Installing kubeadm, kubelet and kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl){:target="_blank"}.


```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### 4. Deploy a Kubernetes cluster with Pod network flannel (on master VM)

The Pod network I chose is [flannel](https://github.com/coreos/flannel). Please refer to [Installing a Pod network add-on](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network){:target="_blank"}.

```bash
kubeadm init --pod-network-cidr=10.244.0.0/16

mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f kube-flannel.yml
```

**NOTE: https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml is not workable**.

I have copied a workable flannel to [https://raw.githubusercontent.com/yuezhizizhang/kube-flannel/master/kube-flannel.yml](https://raw.githubusercontent.com/yuezhizizhang/kube-flannel/master/kube-flannel.yml){:target="_blank"}. Please refer to [Stackoverflow Discussion](https://stackoverflow.com/questions/58024643/kubernetes-master-node-not-ready-state){:target="_blank"} to learn more. 

Now, the kubernetes cluster is ready. Run command to check if every pod is running.

```bash
kubectl get pods -n=kube-system
```

Print the join command with:

```bash
kubeadm token create --print-join-command
```

### 4. Join worker node (on worker VM)

Print the join command:

```bash
kubeadm token create --print-join-command
```

Copy the join command and run it.

**Trouble Shooting**

In some cases, the worker node failed to pull the image of flannel. So after joining the cluster, the status of the worker node is "Failed to pull image "quay.io/coreos/flannel:v0.11.0-amd64". Please refer to [Where I can get docker image of flannel](https://github.com/coreos/flannel/issues/1223){:target="_blank"} to learn more.

```bash
wget https://github.com/coreos/flannel/releases/download/v0.11.0/flanneld-v0.11.0-amd64.docker
docker load < flanneld-v0.11.0-amd64.docker
```

Now, run command to check both the worker node is ready.

```bash
kubectl get nodes
```

Congratulations! Your Kubernetes cluster is deployed. 

### 5. Deploy Tanzu Octant (on master node)

Let's deploy a Pod service [Tanzu Octant](https://github.com/vmware-tanzu/antrea/blob/master/docs/octant-plugin-installation.md){:target="_blank"}. It's a Kubernetes dashboard.

```bash
kubectl create secret generic octant-kubeconfig --from-file=/etc/kubernetes/admin.conf -n kube-system
kubectl apply -f https://raw.githubusercontent.com/vmware-tanzu/antrea/master/build/yamls/antrea-octant.yml
```

Get the NodePort of the octant pod service:

```bash
kubectl describe service antrea-octant -n kube-system
```

Now, you are able to monitor your Kubernetes cluster with <span style="text-decoration: underline;">http://&lt;master_node_ip_address&gt;:&lt;NodePort&gt;</span>.