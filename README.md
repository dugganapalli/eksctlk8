# eksctlk8
eksctl installation
=====================
step(1): launch ec2 instance
-------
step(2): attach iam role--administrator
-------

step(3):install eksctl
------
eksctl 
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /bin

step(4):   kubectl: installation
--------
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(<kubectl.sha256) kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
sudo chmod +x ./kubectl
sudo mv kubectl /bin



step(5):iam-authenticator
-------
curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.8/2020-09-18/bin/linux/amd64/aws-iam-authenticator
chmod +x ./aws-iam-authenticator
mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
aws-iam-authenticator help

step(6):aws cli
-------

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install


step(7):create the cluster 
-------
eksctl create cluster ----


eksctl create cluster \
--name prod-cluster \
--version 1.18 \
--region us-east-1 \
--nodegroup-name linux \
--nodes 3 \
--nodes-min 2 \
--nodes-max 3 \
--with-oidc \
--ssh-access \
--ssh-public-key test \
--managed







-=======================================================
#!/bin/bash
yum update -y
yum install wget -y && yum install unzip -y
# installing eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /bin
eksctl version
# installing IAM authenticator
curl -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/aws-iam-authenticator /tmp
cd /tmp chmod +x ./aws-iam-authenticator
mv /tmp/aws-iam-authenticator /bin
# installing aws cli
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
# installing kubectl
curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/kubectl
chmod +x ./kubectl
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
kubectl version --short --client


===============================================
eksctl create cluster \
--name k8-cluster \
--version 1.18 \
--region ap-south-1 \
--nodegroup-name linux \
--nodes 3 \
--nodes-min 2 \
--nodes-max 3 \
--with-oidc \
--ssh-access \
--ssh-public-key ansible \
--managed


to check the cluster name
----------------------------
eksctl get clusters --region us-east-1


kubectl get nodes
kubectl get pods
kubectl get service
kubectl get deployment
kubectl get secret
kubectl get configmap
kubectl get pv
kubectl get pvc
kubectl get rc
kubectl get rs
kubectl get daemonset
kubectl get statefulset


orchestration
================
To mangae multiple  nodes and multiple  containers
1)kubernetes

K8 architecture
----------------

why k8? why not eksctl
-----------------------
ECS is only for aws
k8 can anywhre local,aws,azure,gcp,onprime
               -------------------------

local: single node cluster---minicube
--------
for testing and development

Docker :maintain all containers manullay
K8: mainatain by k8


k8 architecture
==============
1)we interact with master only and master interact with node
2)kubectl(client)
3)kubeconfig: (homedirectory/.kube/config)
  master IP
  master port
  tls creat
  permission
4)API SERVER:=authenticator+autorization(responsible to connect with clients)
5)shedular= select the nodes for pod deployments(schedule pods to node)
6)ETCD:database (keyvalue pair database)(total information how may nodes ,pods,etc..,)
7)controller(pod/node/service/autosclar...etc..)
8)kubelet =agent between master and worker
9)kubeproxy= sends the traffic from outside  to pods
10)Docker


master                                               worker/node/minion
--------------                                       --------------------  
API SERVER                                           to run pods/apps
schedular                                            kubelet
etcd                                                 kubeproxy  
controller     


Deployment of image(objects)
----------------------------
pod
deployment=======mostly used
replication set/replication controller(old)
daemonset
statefulset

networking(objects)----to access pods to outside
-------------------

service(to group pods and expose to outside) will act as a load balancer and  entrypoint to access pods
                            -------------                   -------------
ingress

storage(objects)
------------------
storageclass(like ebs volume/disc ))
persistant volume claim----attaches/mounts to deployment objects

Secrets(object)/Envionment Variables
------------------------------------
ConfigMap(plain)
Secret (encrypt)



yaml file(hello.yaml)
----------
apiversion: v1
kind: deployment
metadata: 
  name: hello-world
  lables: 
    name:
    value:
spec:
  replicas: 1
  strategy: rollout
  containers:
    image: ecr-repo/image:1
    name:
    port:
    volumes:
    env:
    secrets:

kubectl apply -f hello.yaml


1)helloworld(deployment)----internally wiil create pod --- we ctreate service to access out side---helloworld



