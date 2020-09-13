# Google Cloud Fundamentals: Getting Started with Compute Engine
## Task 1: Sign in to the Google Cloud Platform (GCP) Console
> - gcloud auth login <email_address> and press Enter
> - Enter password and press Enter

## Task 2: Create a virtual machine using the GCP Console
**Prerequisites:**
- check allow-http
        This creates a firewall rule allowing incoming traffic from any ip address on the internet
        a target tag (http-server) is attached to both the firewall rule and the virtual machine

> gcloud compute instances create my-vm1 --zone us-central1-a --machine-type e2-medium --tags=http-server\ >--image=debian-9-stretch-v20200910 --image-project=debian-cloud 

#### create firewall rule to allow http traffic   
> gcloud compute firewall-rules create allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server


## Task 3: Create a virtual machine using the gcloud command line
#### List all the zones available in the assigned Region (us-central-1)
> gcloud compute zones list | grep us-central1

#### Choose and configure  a zone from the assigned region other than the assigned zone as default
#### The assigned zone is us-central1-a 

> gcloud config set compute/zone us-central1-b

#### create the virtual machine
> gcloud compute instances create "my-vm-2" --machine-type "n1-standard-1" --image-project "debian-cloud" \
--image "debian-9-stretch-v20190213" \
--subnet "default"

# Task 4: Connect between VM instances
#### SSH into my-vm-2
> gcloud compute ssh my-vm-2

#### ping the second vm (my-vm-2) 
> ping my-vm-1

#### SSH into my-vm-1
> ssh my-vm-1 -y

#### ping the other vm (my-vm-2) 
> ping my-vm-2

#### Install Nginx web server
> sudo apt-get install nginx-light -y

#### Open the home page of Nginx server in nano
> sudo nano /var/www/html/index.nginx-debian.html

#### Replace YOUR_NAME with your name. 
> 1. Press Ctrl + \
> 2. Enter search string YOUR_NAME
> 3. Enter replacement string AHMED LUKMAN
> 4. Press Ctrl+O and then press Enter to save your edited file
> 5. Press Ctrl+X to exit the nano text editor.

#### Confirm that the web server is running (in my-vm-1)
> curl http://localhost/

#### Exit my-vm-1 and go back to my-vm-2
> exit

#### visit the external ip address of my-vm-1
``` bash
$EXTERNAL_IP=gcloud compute instances describe my-vm-2 --format='get(networkInterfaces[0].accessConfigs[0].natIP)'

curl $EXTERNAL_IP
 ```

