# hlf-k8s
Hyperledger fabric on kubernetes
###### Credits:wave: [aclodfan](https://github.com/acloudfan)

#### Setup-minikube-Windows 

##### Pre-requisites
1. minikube(https://github.com/kubernetes/minikube/releases/latest)
2. kubectl (https://storage.googleapis.com/kubernetes-release/release/v1.18.0/bin/windows/amd64/kubectl.exe)
3. Virtual box(must have virtualization enabled)

>Rename downloaded minikube file to minikube.exe and add those downloaded files(minikube.exe and kubectl.exe) to your PATH.

##### Steps
1. Create minikube cluster

```
minikube start --memory=4096

```
2. Add storage class for minikube
```
git clone https://github.com/acloudfan/HLF-K8s-Cloud.git
cd HLF-K8s-Cloud
```

 >create a file ```k8s-storage-class.yaml``` with the contents below  in the root directory where all other yaml files are located.
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: general
# Adjust provisioner for the cloud setup
provisioner: k8s.io/minikube-hostpath 
parameters:
  type: pd-ssd
```

3. Launch the pods
```
kubectl apply -f .
```
[Next Steps](#Starting-Network)
### Setup-GKE Cloud

Create a K8s cluster

-Kubernetes Statefulset type 3-pod cluster on GKE

1. orderer Pod
2. Acme-peer Pod
3. Budget-Peer Pod

Connect your cluster with the cloud shell


-Pushing docker images to pods and launching Peers

```
git clone https://github.com/acloudfan/HLF-K8s-Cloud.git

cd HLF-K8s-Cloud/gcp
kubectl apply -f . # Applies storage class

cd ..
kubectl apply -f .  #Containerizes Three Pods with .yaml files in Repository

```

### Starting-Network

##### 1. Setup Channel and Joining Channel

```
kubectl exec -it acme-peer-0 /bin/bash # Allows you to logging into acme-Peer container cli

./submit-channel-create.sh #Channel Cretion

./join-channel.sh # Acme-peer Joins Channel

peer channel list  #Checks if Peer joined
exit
```
- Budget-Peer Joining Channel 

```
kubectl exec -it budget-peer-0 /bin/bash 

./fetch-channel-block.sh #Fetches channel.block to budget peer container

./join-channel.sh

peer channel list

exit 
```

##### 2. Installing And Instantiating Chaincode

```
kubectl exec -it acme-peer-0 /bin/bash

./cc-test.sh install  #installs chaincode on acme-peer
./cc-test.sh instantiate

kubectl exec -it budget-peer-0 /bin/bash 
./cc-test.sh install  #installs chaincode on budget-peer

```
##### 3. Invoking and Querying Chaincode

```
kubectl exec -it acme-peer-0 /bin/bash

./cc-test.sh query  #Queries from acme peer
./cc-test.sh invoke #Invoke from acme peer

kubectl exec -it budget-peer-0 /bin/bash 

./cc-test.sh query  #Queries from budget peer
./cc-test.sh invoke #Invoke from budget peer
```
