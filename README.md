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

```gcloud compute instances create ExampleVm --zone us-central1-c```

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


### Create VM-Template

#### Make startup.sh for VM-Template

It is just a normal batch shell-script not specialized to VM.

```
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
```

### Make VM-Template

```
gcloud compute instance-templates create nginx-template --metadata-from-file startup-script=startup.sh
```
