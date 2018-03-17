Introduce
==== 

List useful CLIs when work with Google Cloud Platform(GCP) commonly. 

## Ready

### Local computer init

```gcloud init```

Use this CLI on local computer to initilize global configuration of GCP on the local computer. You will see clear instructions for use, simple and clear. 

1. Login with Google account
2. Connect to a Cloud project

### Auth/Login

```gcloud auth list```

List current auth-user and you'll see login email etc.

## Project

### Show project

```gcloud config list project```

List current connected project.

### Set/connect project

```gcloud config set project hello-world-project```

Connect current GCP-Environment to project ```hello-world-project``` .

## Virtual Computer

### Create VM

```gcloud compute instances create example-vm --zone us-central1-c```

```

You can set the default region and zones that gcloud uses if you are always working within one region/zone and you don't want to append the --zone flag every time. Do this by running these commands :

gcloud config set compute/zone ...

gcloud config set compute/region ...

```

### SSH into VM

```gcloud compute ssh example-vm --zone us-central1-c```

#### SSH into VM and install a webserver

- ```sudo su -```
- ```apt-get update```
- ```apt-get install nginx -y```
- ```ps auwx | grep nginx```  -> For test, you can use external URL to visit the webserver.

### Create disk

### Attach disk

### Find disk

### Format disk

### Mount disk

### Mount disk(after VM restart)


 
