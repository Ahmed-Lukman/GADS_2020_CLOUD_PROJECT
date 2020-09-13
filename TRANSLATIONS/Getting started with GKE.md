# Google Cloud Fundamentals: Getting Started with GKE
## Task 1: Sign in to the Google Cloud Platform (GCP) Console 
``` bash
- gcloud auth login <email_address> and press Enter
- Enter password and press Enter
 ```

## Task 2: Confirm that needed APIs are enabled
> Enable Kubernetes Engine API
``` sh
gcloud services enable containers.googleapis.com
```

> Enable Container Registry API
``` sh
gcloud services enable container-registry.googleapis.com
```

 ## Task 3: Start a Kubernetes Engine cluster
 #### 1. For convenience, place the zone that Qwiklabs assigned you to into an environment variable called MY_ZONE
 ``` sh
export MY_ZONE=us-central1-a

 ```
 #### Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster webfrontend and configure it to run 2 nodes:

 ``` sh
gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2
 ```
 #### check your installed version of Kubernetes using the kubectl version command:

 ``` sh
kubectl version
 ```
 #### View your running nodes in the GCP Console. On the Navigation menu (Navigation menu), click Compute Engine > VM Instances.
 ``` sh
gcloud compute instances list
 ```
 #### Task 4: Run and deploy a container
 ``` sh
 kubectl create deploy nginx --image=nginx:1.17.10
 ```
 #### View the pod running the nginx container:

``` sh
kubectl get pods
```

#### Expose the nginx container to the Internet:

``` sh
kubectl expose deployment nginx --port 80 --type LoadBalancer
```

#### View the new service:

``` 
kubectl get services
```

#### Scale up the number of pods running on your service:

``` 
kubectl scale deployment nginx --replicas 3
```

#### Confirm that Kubernetes has updated the number of pods:

``` 
kubectl get pods
```

#### Confirm that your external IP address has not changed:

``` 
kubectl get services
```

Return to the web browser tab in which you viewed your cluster's external IP address. Refresh the page to confirm that the nginx web server is still responding.


 ``` sh

 ```

 ``` sh

 ```