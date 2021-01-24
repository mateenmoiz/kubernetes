# Kubernetes - Installation on Master Node

**Step 1:** Login to the master node  and execute the command to get root access. 
```sh
$  sudo -i
```
If root access is not available then arrange user sudo access using visudo as root. Now using root user execute the steps 2 to  10

**Step 2:** Add **k8smaster** host entry in /etc/hosts file (if executing the command on the worker node, still master node ip need to be inserted). Example
```sh
$  vi /etc/hosts
```

Add the following line in hosts file 

```sh
172.31.46.172   k8smaster 
```
where 172.31.46.172 is the ip of the master node.

**Step 3:** Run the following command to update and upgrade the system.
```sh
$  apt-get update && apt-get upgrade -y
```
**Step 4:** Install vim editor 
```sh
$  apt-get install -y vim
```
**Step 5:** Install docker
```sh
$  apt-get install -y docker.io
```

**Step 6:** Add a new repo for kubernetes in file /etc/apt/sources.list.d/kubernetes.list  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Execute the vi command to edit the file 
```sh
$  vi /etc/apt/sources.list.d/kubernetes.list
```

Add the following line in kubernetes.list 
    
```sh    
    deb https://apt.kubernetes.io/ kubernetes-xenial main
```
**Step 7:** Add a GPG key for the packages using below command, when the command execute successfully it will give output as "OK"
```sh
$  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg |apt-key add -
```
**Step 8:** Update new repo using command
```sh
$  apt-get update 
```
**Step 9:** Install binaries using below command
```sh
$  apt-get install -y kubeadm=1.18.1-00 kubelet=1.18.1-00 kubectl=1.18.1-00
```
**Step 10:** Hold binaries version update
```sh
$  apt-mark hold kubeadm kubectl kubelet
```
**Step 11:** Logout as root
```sh
$  logout
```
**Step 12:** Create cluster using kubeadm command using one of the following two methods. 
         
**Method#1 :** Using Command
```sh
$  sudo kubeadm init --control-plane-endpoint k8smaster:6443 --kubernetes-version 1.18.1 --pod-network-cidr 192.168.0.0/16 --upload-certs|tee kubeadm-init.out
```
**Method#2 :** Using yaml file

Create the following yaml file with the following data
```sh	
$	vi kubeadm-config.yaml
```

```sh
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
kubernetesVersion: v1.18.1
controlPlaneEndpoint: "k8smaster:6443"
networking:
  podSubnet: 192.168.0.0/16
```
Run below command to create cluster
```sh
$  sudo kubeadm init --config=kubeadm-config.yaml --upload-certs | tee kubeadm-init.out
```

**Step 13:** Execute the following three command as expelled out by the Step 12.
```sh
$  mkdir -p $HOME/.kube
$  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**Step 14:** Apply Network config
```sh
$  kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

# Kubernetes - Installation on Worker Node

Execute the above Steps from 1 to 11 only. 

Also execute the join command expelled from execution of Step 12 of the master node installation above
