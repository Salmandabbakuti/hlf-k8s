# hlf-k8s
Hyperledger fabric on kubernetes
>Credit goes to acloudfan


### Setup

-Kubernetes Statefulset type 3-pod cluster on GKE
1. orderer Pod
2. Acme-peer Pod
3. Budget-Peer Pod

Create a K8s cluster

Connect your cluster with the cloud shell


-Pushing docker images to pods and launching Peers

```
git clone https://github.com/acloudfan/HLF-K8s-Cloud.git

cd HLF-K8s-Cloud/gcp
kubectl apply -f . # Applies storage class

cd ..
kubectl apply -f .  #Containerizes Three Pods with .yaml files in Repository

```

-Starting Fabric Network

1. Setup Channel and Joining Channel

```
kubectl exec -it acme-peer-0 /bin/bash # Allows you to logging into acme-Peer container cli

./submit-channel-create.sh #Channel Cretion

./join-channel.sh # Acme-peer Joins Channel

peer channel list  #Checks if Peer joined
exit
```
-Budget-Peer Joining Channel 

```
kubectl exec -it budget-peer-0 /bin/bash 

./fetch-channel-block.sh #Fetches channel.block to budget peer container

./join-channel.sh

peer channel list

exit 
```

### 2. Installing And Instantiating Chaincode

```
kubectl exec -it acme-peer-0 /bin/bash

./cc-test.sh install  #installs chaincode on acme-peer
./cc-test.sh instantiate

kubectl exec -it budget-peer-0 /bin/bash 
./cc-test.sh install  #installs chaincode on budget-peer

```
### 3. Invoking and Querying Chaincode

```
kubectl exec -it acme-peer-0 /bin/bash

./cc-test.sh query  #Queries from acme peer
./cc-test.sh invoke #Invoke from acme peer

kubectl exec -it budget-peer-0 /bin/bash 

./cc-test.sh query  #Queries from budget peer
./cc-test.sh invoke #Invoke from budget peer
```
