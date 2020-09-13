# Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL
## Task 1: Sign in to the Google Cloud Platform (GCP) Console
``` bash
- gcloud auth login <email_address> and press Enter
- Enter password and press Enter
 ```

## Task 2: Deploy a web server VM instance
**Prerequisites:**
- SET instance name to bloghost
- SET Boot disk image to Debian GNU/Linux 9 (stretch).
- CHECK Allow http traffic
    This creates a firewall rule allowing incoming traffic from any ip address on the internet
    a target tag (http-server) is attached to both the firewall rule and the virtual machine

#### 1. Create a starup script 
> nano startup.sh
Paste the following code

``` bash
apt-get update
apt-get install apache2 php php-mysql -y
service apache2 restart

```
- Press Ctrl + O to save
- Press Ctrl + X to exit

#### 2. Create the vm and attach startup script and http-server tag
``` bash 
gcloud compute instances create bloghost --zone us-central1-a image=debian-9-stretch-v20200910\
--tags=http-server --image-project=debian-cloud  --metadata-from-file startup-script=startup.sh
```

#### Create firewall rule to allow http traffic   
```sh 
gcloud compute firewall-rules create allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
```




## Task 3: Create a Cloud Storage bucket using the gsutil command line
#### 1. Create a bucket using gsutil Command Line
_This is a multi-regional bucket in US_
``` export $LOCATION=US  
gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID 
```

#### 2. Retrieve a banner image from a publicly accessible Cloud Storage location:
```bash 
gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png
```

#### 3. Copy the banner image to your newly created Cloud Storage bucket:
``` bash
gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png

``` 
#### 4. Modify the Access Control List of the object you just created so that it is readable by everyone
``` bash
gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
``` 



## Task 4: Create the Cloud SQL instance
_**Prerequisites**_
_instance id: blog-db_, 
_database engine: MySql_
``` bash
gcloud sql instances create blog-db  --database-version=MYSQL_5_7 --root-password=mypassword123 --region=us-central1 \
--zone=us-central1-a
```
#### From the SQL instances details page, copy the Public IP address for your SQL instance to a text editor for use later in this lab.

_Instead the public ip address will be stored in an environmental variable  ``` BLOG_EXT_IP ```_
``` bash
$BLOG_EXT_IP=gcloud sql instances describe blog-db --format='get(networkInterfaces[0].ipAddress)'

```
#### ADD USER ACCOUNT
``` bash
gcloud sql users set-password blog-db-user --host=% --instance=blog-db --prompt-for-password
```
####  Add network
``` sh
gcloud sql instances patch blog-db --authorized-networks=$BLOG_EXT_IP/32
```
    
## Task 5: Configure an application in a Compute Engine instance to use Cloud SQL
#### ssh into blog-host instance
``` bash
gcloud compute ssh blog-host

```
#### change directory to the document root of the web server

``` bash
cd /var/www/html
```
#### Use the nano text editor to edit a file called index.php

``` bash
sudo nano index.php
```
#### Paste the content below into the file:
``` html
<html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<h1>Welcome to my excellent blog</h1>
<?php
$dbserver = "CLOUDSQLIP";
$dbuser = "blogdbuser";
$dbpassword = "DBPASSWORD";
// In a production blog, we would not store the MySQL
// password in the document root. Instead, we would store it in a
// configuration file elsewhere on the web server VM instance.

$conn = new mysqli($dbserver, $dbuser, $dbpassword);

if (mysqli_connect_error()) {
        echo ("Database connection failed: " . mysqli_connect_error());
} else {
        echo ("Database connection succeeded.");
}
?>
</body></html>
```
> Press Ctrl+O, and then press Enter to save your edited file.

> Press Ctrl+X to exit the nano text editor.
#### Restart the web server:
``` bash
sudo service apache2 restart
```

#### Visit the bloghost VM instance's external IP address followed by /index.php
``` curl $BLOG_EXT_IP/index.php ```
#### Use the nano text editor to edit index.php again.
``` sudo nano index.php ```
#### replace CLOUDSQLIP with the Cloud SQL instance Public IP address that you noted above. 
``` CLOUDSQLIP=$BLOG_EXT_IP ```
#### replace DBPASSWORD with the Cloud SQL database password that you defined above. 
``` bash
DBPASSWORD="mypassword123" 
```
>Press Ctrl+O, and then press Enter to save your edited file.

>Press Ctrl+X to exit the nano text editor.

Restart the web server:
``` sudo service apache2 restart  ```
#### Revisit the bloghost VM instance's external IP address followed by /index.php again
``` curl BLOG_EXT_IP/index.php ```

## Task 6: Configure an application in a Compute Engine instance to use a Cloud Storage object
#### Copy the URL behind the link icon that appears in that object's Public access
_The url will be saved in the variable ```OBJ_URL```_
``` sh
$OBJ_URL=gsutil ls -l gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png --format='get(networkInterfaces[0].ipAddress'
```
#### ssh into the bloghost VM instance
``` gcloud compute ssh blog-host ```
#### Navigate to the document root of the web server:
```
cd /var/www/html
```
#### Use the nano text editor to edit index.php:
``` bash
sudo nano index.php
```

>Use the arrow keys to move the cursor to the line that contains the h1 element.
>Press Enter to open up a new, blank screen line,
#### then paste the URL you copied earlier into the line.
$OBJ_URL

#### Paste this HTML markup immediately before the URL:

``` <img src=' ```

>Place a closing single quotation mark and a closing angle bracket at the end of the URL:

``` '> ```

The resulting line will look like this:
``` html
<img src='$OBJ_URL'>
```
>Press Ctrl+O, and then press Enter to save your edited file.

>Press Ctrl+X to exit the nano text editor.

#### Restart the web server:
 ```  sudo service apache2 restart  ```