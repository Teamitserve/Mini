# Project Explanation

This the complete process step by step with commands and the details

<h2> 1.Installing Minikube </h2>
When you use Linux the below commands are in Binary below

```
 curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
 sudo install minikube-linux-amd64 /usr/local/bin/minikube
 minikube version
```

<h2>2.Installing Kubectl</h2>
The below commands are used in the linux to install Kubectl

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

<h2>3.Now create the minikube cluster</h2>

```
  minikube start
```

Once the cluster has been created, you can verify its status by entering

```
minikube status
```

<h2>4.Create Namespace</h2>

```
kubectl create namespace jenkins
```

<h2>5.Installing Helm</h2>

```
wget https://get.helm.sh/helm-v3.5.4-linux-amd64.tar.gz
tar -zxvf helm-v3.5.4-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```

To confirm helm is installed or not use 

```
helm help
```

<h2>6.Configure Helm</h2>

```
helm repo add jenkinsci https://charts.jenkins.io
helm repo update
```

To check on the whether the repo is added or not 

```
helm search repo jenkinsci
```

<h1> Steps before installing jenkins <h1>
  1.Creating presistent volume
  2.Creating service account
  
  <h2>Creating presistant volume</h2>
  Run the above file by the following command
  
  ```
  kubectl apply -f jenkins-volume.yaml
  ```
  
  <h2>Create service account</h2>
  Run the below commands for creating services
  
  ```
  kubectl apply -f jenkins-sa.yaml
  ```

<h1>Installing Jenkins</h1>
Use values.yaml for using helm chart

```
chart=jenkinsci/jenkins
helm install jenkins -n jenkins -f jenkins-values.yaml $chart
```

Once it is deployed then get the username, password and jenkins URL

```
jsonpath="{.data.jenkins-admin-password}"
secret=$(kubectl get secret -n jenkins jenkins -o jsonpath=$jsonpath)
echo $(echo $secret | base64 --decode)
jsonpath="{.spec.ports[0].nodePort}"
NODE_PORT=$(kubectl get -n jenkins -o jsonpath=$jsonpath services jenkins)
jsonpath="{.items[0].status.addresses[0].address}"
NODE_IP=$(kubectl get nodes -n jenkins -o jsonpath=$jsonpath)
echo http://$NODE_IP:$NODE_PORT/login
```

Once it is up and running get the external port by

```
kubectl get pods -o wide -n jenkins
```

<h2>Fort Forwarding</h2>

```
kubectl -n jenkins port-forward <pod_name> 8085:8085
```

<h2>Deployment of jenkins</h2>

```
kubectl create -f jenkins-deployment.yaml -n jenkins
kubectl get deployments -n jenkins
```

<h2> Granting access to Jenkins service</h2>

```
kubectl create -f jenkins-service.yaml -n jenkins
```
