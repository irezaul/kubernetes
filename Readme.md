# How to install putty on macOs
> open terminal and type the code.
```bash
brew install putty
```
![Putty install on macOS](https://user-images.githubusercontent.com/77927449/124318696-d9c5d680-db9a-11eb-89d3-13adc4ebf047.png)

# Now Create 2/3 ubuntu server on VirtualBox

### installing Docker on all 3 Machines

##### First need to update the package list in all 3 machines
```bash 
sudo apt-get update
`````

#### 	•	Next install Docker on all 3 machines
```bash
sudo apt-get install docker.io
````  
•	Next check the version of Docker on all 3 machines
```bash
docker ––version
```
####	Next set Docker to launch at boot in all 3 machines
```bash
sudo systemctl enable docker
```
#### Next verify if Docker is running on all 3 machines - `Install Kubernetes all 3 machin`
```bash
sudo systemctl status docker
```
#### Since you are downloading Kubernetes from a non-standard repository, it is essential to ensure that the software is authentic. This is done by adding a signing key 

```bash	
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
##### Next
```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add 
```
#### Since Kubernetes is not included in the default repo, add software repo:
```bash 
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
```
#### Enter the following commands to install KUBEADM, KUBELET, KUBECTL:
```bash 
sudo apt-get install kubeadm kubelet kubectl
sudo apt-mark hold kubeadm kubelet kubectl
```
#### Verify the installation: 
```bash 
kubeadm version 
```
#### In order to start Kubernetes deployment start by disabling swap memory for each server:
```bash 
sudo swapoff –a
```
#### Assign unique hostname for each server node or machine:
```bash
sudo hostnamectl set-hostname master-node
sudo hostnamectl set-hostname worker01
sudo hostnamectl set-hostname worker02
```
#### Initialize Kubernetes on Master Node ONLY. Please note cidr 10.244.0.0/16 is designated for Flannel network. This network is required for Pods to communicate with each other. 
```bash 
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --ignore-preflight-errors=all
```
#### Please copy the printout kubeadm join command (at the end of the printout) for later use. This command is unique to each cluster and will be used for joining other nodes to the cluster. Need to run the following commands to complete deployment on Master node: 
```bash	
mkdir -p $HOME/.kube 
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
#### A Pod Network is a way to allow communication between different nodes in the cluster. This tutorial uses the flannel virtual network. Enter the following command on Master node:

```bash 
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
##### use code when `kubernetes` host if error show -
```bash 
kubeadm reset 
```

#### If show error IP address not found like that - so use this code- exmaple <br>(`The connection to the server 192.168.x.x:6443 was refused - did you specify the right host or port? `)

```bash 
sudo cp /etc/kubernetes/admin.conf ~/.kube/config && sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
#### Allow the process to complete and verify that everything is running and communicating:
```bash 
kubectl get pods --all-namespaces
```
#### Check the nodes on the current cluster by running the following command in Master node:
```bash 
kubectl get nodes
```
#### As mentioned in the earlier steps (see Kubernetes deployment), now kubeadm join command can be used in each worker node to connect them to the cluster. Run the command on each Worker node (the token part is excluded, since your token and IP should be unique):
```bash 
sudo kubeadm join 172.31.x.x:6443 --token
```
#### if you see the error use this code
```bash
kubeadm reset
```
#### Now run the “kubectl get nodes” on the Master node to check node status
```bash
kubectl get nodes
```
