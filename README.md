Introduce
==== 

List useful CLIs when work with Google Cloud Platform(GCP) commonly. 

## Ready

### gcloud

```gcloud -h``` -> simple help

```gcloud config --help``` or ```gcloud help config``` ->  long, detailed help
 
```cd $HOME``` -> Change your current working directory.

### gsutil

```gsutil mb gs:/my-very-unique-bk-name``` -> Make a bucket, the name must be unique global(really, distinct to the others in the whole world).

```gsutil mb -l EU gs://$DEVSHELL_PROJECT_ID``` -> In Cloud Shell, make a bucket for EU region and use name with environement variable, the $DEVSHELL_PROJECT_ID.

> Use linux command, you will know what it is.
```echo $DEVSHELL_PROJECT_ID```

```gsutil defacl ch -u AllUsers:R gs://$DEVSHELL_PROJECT_ID``` -> Grant ACL for "R" (read for all users)

```gsutil rb gs:/my-very-unique-bk-nam``` -> Remove a bucket.

```gsutil cp test-file.dat gs://my-very-unique-bk-name``` -> Copy one file to the bucket.

```gsutil --help``` -> Help, get help to other flags and options.

### Environment

```gcloud config list```

```gcloud config list --all```

Get configuration of current environment, use ```--all``` for all properties.


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

```
Create single VM.

gcloud compute instances create ExampleVm --zone us-central1-c
```

```
gcloud compute instances create webserver4 --image-family debian-9 --image-project
 debian-cloud --tags int-lb --zone $MY_ZONE2 --subnet default --metadata startup-script-url="gs://cloud-training/archinfra/mystartupscri
pt",my-server-id="WebServer-4"
```

```
Create a few Nginx instances using a Bitnami Nginx Stack image, which includes a complete PHP, MySQL and Nginx development environment.

for i in {1..3}; \
do \
  gcloud compute instances create "nginxstack-$i" \
  --machine-type "f1-micro" \
  --tags nginxstack-tcp-443,nginxstack-tcp-80 \
  --zone us-central1-f \
  --image   "https://www.googleapis.com/compute/v1/projects/bitnami-launchpad/global/images/bitnami-nginxstack-1-10-2-0-linux-debian-8-x86-64" \
  --boot-disk-size "200" --boot-disk-type "pd-standard" \
  --boot-disk-device-name "nginxstack-$i"; \
done

create a firewall rule to allow external traffic to the instances:

gcloud compute firewall-rules create nginx-firewall \
 --allow tcp:80,tcp:443 \
 --target-tags nginxstack-tcp-80,nginxstack-tcp-443
```

> Notice that you can't change the machine type, the CPU platform, or the zone. You can add network tags and allow specific network traffic from the internet through firewalls.Some properties of a VM are integral to the VM are established when the VM is created, and cannot be changed. Other properties can be edited. You can add additional disks and you can also determine whether the boot disk is deleted when the instance is deleted. Normally the boot disk defaults to being deleted automatically when the instance is deleted. But sometimes you will want to override this behavior. This feature is very important because you cannot create an image from a boot disk when it is attached to a running instance. So you would need to disable Delete boot disk when instance is deleted to enable creating a system image from the boot disk. 
    
> When you connect via SSH to an instance from your browser, you need to allow SSH from Cloud Platform resources, so you must allow connections from either any IP address or from Google's IP address range, which you can get from Public SPF records. If you want to restrict SSH access to just your IP address, you need to SSH from a terminal session. When instances do not have external IP addresses, they can only be reached by other instances on the network or via a managed VPN gateway. In this case, the bastion VM serves as a management and maintenance interface to the webserver VM.

### Create VM with template

#### Make startup.sh for VM-Template

It is just a normal batch shell-script not specialized to VM.

```
# This script is for install nginx webserver.
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
```

#### Create VM-Template

```
gcloud compute instance-templates create nginx-template --metadata-from-file startup-script=startup.sh
```



### Make a target-pool for holding VMs (target-pools)

#### A pool

```gcloud compute target-pools create nginx-pool```

#### 2 instances in pool

```gcloud compute instance-groups managed create nginx-group --base-instance-name nginx --size 2 --template```

```gcloud compute instances list```

#### Set named-ports

```gcloud compute instance-groups managed set-named-ports nginx-group --named-ports http:80```

```
You can set the default region and zones that gcloud uses if you are always working within one region/zone and you don't want to append the --zone flag every time. Do this by running these commands :

gcloud config set compute/zone ...
gcloud config set compute/region ...

For example:
gcloud config set compute/zone us-central1-a
gcloud config set compute/region us-central1
```




### List VMs instances

```gcloud compute instances list```

### SSH into VM

```gcloud compute ssh ExampleVm --zone us-central1-c```

#### SSH into VM and install a webserver

- ```sudo su -```
- ```apt-get update```
- ```apt-get install nginx -y```
- ```ps auwx | grep nginx```  -> For test, you can use external URL to visit the webserver.

##### For Apache

- ```sudo apt-get update```
- ```sudo apt-get install apache2 php7.0```
- ```sudo service apache2 restart```

> Make sure that you're using HTTP and not HTTPS for the external IP address!

### Create disk

```gcloud compute disks create ExampleDisk --size=200GB --zone us-central1-c``` -> Create a persistent disk.

Before create a disk, you should have already a VM to which the disk will be attached.

### Attach disk

```gcloud compute instances attach-disk ExampleVm --disk ExampleDisk --zone us-central1-c``` -> Attach disk onto VM.

### Find disk

You must SSH into the VM then you'll see your attach-disks.

- ```gcloud compute ssh ExampleVm  --zone us-central1-c```
- ```ls -l /dev/disk/by-id/```

You will see

```
lrwxrwxrwx 1 root root  9 Feb 27 02:24 google-persistent-disk-0 -> ../../sda
lrwxrwxrwx 1 root root 10 Feb 27 02:24 google-persistent-disk-0-part1 -> ../../sda1
lrwxrwxrwx 1 root root  9 Feb 27 02:25 google-persistent-disk-1 -> ../../sdb
lrwxrwxrwx 1 root root  9 Feb 27 02:24 scsi-0Google_PersistentDisk_persistent-disk-0 -> ../../sda
lrwxrwxrwx 1 root root 10 Feb 27 02:24 scsi-0Google_PersistentDisk_persistent-disk-0-part1 -> ../../sda1
lrwxrwxrwx 1 root root  9 Feb 27 02:25 scsi-0Google_PersistentDisk_persistent-disk-1 -> ../../sdb
```

The default name (attached disk): ```scsi-0Google_PersistentDisk_persistent-disk-1```

If you want a different device name, when you attach the disk, you would specify the device-name parameter. For example, to specify a device name, when you attach the disk you would use the command:

```gcloud compute instances attach-disk ExampleVm --disk ExampleDisk --device-name <YOUR_DEVICE_NAME> --zone us-central1-c```


### Format disk

```sudo mkfs.ext4 -F -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/disk/by-id/scsi-0Google_PersistentDisk_persistent-disk-1```

### Mount disk

- ```sudo mkdir /mnt/mydisk```

- ```sudo mount -o discard,defaults /dev/disk/by-id/scsi-0Google_PersistentDisk_persistent-disk-1 /mnt/mydisk```

### Mount disk(after VM restart)

- ```sudo vi /etc/fstab```
- Append this ```/dev/disk/by-id/scsi-0Google_PersistentDisk_persistent-disk-1 /mnt/mydisk ext4 defaults 1 1```



## Work with routes, firewall rules

### Create firewall

```gcloud compute firewall-rules create www-firewall --allow tcp:80```

### Create forwarding-rules

```gcloud compute forwarding-rules create nginx-lb --region us-central1 --ports=80 --target-pool nginx-pool```

```gcloud compute forwarding-rules list```

### Create the health-checks

```gcloud compute http-health-checks create http-basic-check```

> Google Cloud Platform (GCP) Virtual Private Cloud (VPC) networks have an internal DNS service that allows you to use instance names instead of instance IP addresses to refer to Compute Engine virtual machine (VM) instances. Each instance has a metadata server that also acts as a DNS resolver for that instance. DNS lookups are performed for instance names. The metadata server itself stores all DNS information for the local network and queries Google's public DNS servers for any addresses outside of the local network. An instance is not aware of any external IP address assigned to it. Instead, the network stores a lookup table that matches external IP addresses with the internal IP addresses of the relevant instances.

### Backend

```gcloud compute backend-services create nginx-backend --protocol HTTP --http-health-checks http-basic-check --global```

```gcloud compute backend-services add-backend nginx-backend --instance-group nginx-group --instance-group-zone us-central1-a --global```

```gcloud compute url-maps create web-map --default-service nginx-backend```

```gcloud compute target-http-proxies create http-lb-proxy --url-map web-map```

```gcloud compute forwarding-rules create http-content-rule --global --target-http-proxy http-lb-proxy --ports 80```

```gcloud compute forwarding-rules list```


## Examples and practice

#### GCP container engine and kubernetes
#### GCP compute engine 

 

## Deployment manager

See: https://github.com/XinyueZ/google-cloud-things/blob/master/deployment-manager.md