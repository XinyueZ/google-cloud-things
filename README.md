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


gWelcome to Cloud Shell! Type "help" to get started.
google308597_student@cloudshell:~$ gcloud auth list
          Credentialed Accounts
ACTIVE  ACCOUNT
*       google308597_student@qwiklabs.net
gWelcome to Cloud Shell! Type "help" to get started.
google308597_student@cloudshell:~$ gcloud auth list
          Credentialed Accounts
ACTIVE  ACCOUNT
*       google308597_student@qwiklabs.net
To set the active account, run:
    $ gcloud config set account `ACCOUNT`
google308597_student@cloudshell:~$ gcloud config list project
[core]
project (unset)
Your active configuration is: [cloudshell-7632]
google308597_student@cloudshell:~$ gcloud config set project qwiklabs-gcp-1dbce9c2a67de1f9                
Updated property [core/project].
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud config list project
[core]
project = qwiklabs-gcp-1dbce9c2a67de1f9
Your active configuration is: [cloudshell-7632]
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud config set compute/zone us-central1-a
Updated property [compute/zone].
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud config set compute/region us-central1
Updated property [compute/region].
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ vi startup.sh
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute instance-templates create nginx-template --metadata-from-file startup-script=startup.sh
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/global/instanceTemplates/nginx-template].
NAME            MACHINE_TYPE   PREEMPTIBLE  CREATION_TIMESTAMP
nginx-template  n1-standard-1               2018-03-26T14:41:35.802-07:00
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute target-pools create nginx-pool
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/regions/us-central1/targetPools/nginx-pool].
NAME        REGION       SESSION_AFFINITY  BACKUP  HEALTH_CHECKS
nginx-pool  us-central1  NONE
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute instance-groups managed create nginx-group --base-instance-name nginx --size 2 --template
 nginx-template --target-pool nginx-pool
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/zones/us-central1-a/instanceGroupManagers/nginx-group].
NAME         LOCATION       SCOPE  BASE_INSTANCE_NAME  SIZE  TARGET_SIZE  INSTANCE_TEMPLATE  AUTOSCALED
nginx-group  us-central1-a  zone   nginx               0     2            nginx-template     no
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute instances list
NAME        ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
nginx-8l4l  us-central1-a  n1-standard-1               10.128.0.3   35.226.32.141  RUNNING
nginx-n7j5  us-central1-a  n1-standard-1               10.128.0.2   35.184.164.96  RUNNING
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute firewall-rules create www-firewall --allow tcp:80
Creating firewall...\Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/global/firewalls/www-firewall].
Creating firewall...done.
NAME          NETWORK  DIRECTION  PRIORITY  ALLOW   DENY
www-firewall  default  INGRESS    1000      tcp:80
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute forwarding-rules create nginx-lb --region us-central1 --ports=80 --target-pool nginx-pool
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/regions/us-central1/forwardingRules/nginx-lb].
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute forwarding-rules list
NAME      REGION       IP_ADDRESS    IP_PROTOCOL  TARGET
nginx-lb  us-central1  35.202.93.19  TCP          us-central1/targetPools/nginx-pool
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute http-health-checks create http-basic-check
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/global/httpHealthChecks/http-basic-check].
NAME              HOST  PORT  REQUEST_PATH
http-basic-check        80    /
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute instance-groups managed set-named-ports nginx-group --named-ports http:80
Updated [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/zones/us-central1-a/instanceGroups/nginx-group].
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute backend-services create nginx-backend --protocol HTTP --http-headth-checks http-basic-check --global
ERROR: (gcloud.compute.backend-services.create) unrecognized arguments:
  --http-headth-checks (did you mean '--http-health-checks'?)
  http-basic-check
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute backend-services create nginx-backend --protocol HTTP --http-headth-checks http-basic-check--global
ERROR: (gcloud.compute.backend-services.create) unrecognized arguments:
  --http-headth-checks (did you mean '--http-health-checks'?)
  http-basic-check--global
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute backend-services create nginx-backend --protocol HTTP --http-health-checks http-basic-check --global
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/global/backendServices/nginx-backend].
NAME           BACKENDS  PROTOCOL
nginx-backend            HTTP
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute backend-services add-backend nginx-backend --instance-group nginx-group --instance-group-zone us-central1-a --global
Updated [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/global/backendServices/nginx-backend].
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute url-maps create web-map --default-service nginx-backend
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/global/urlMaps/web-map].
NAME     DEFAULT_SERVICE
web-map  backendServices/nginx-backend
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute target-http-proxies create http-lb-proxy --url-map web-map
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/global/targetHttpProxies/http-lb-proxy].
NAME           URL_MAP
http-lb-proxy  web-map
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute forwarding-rules create http-content-rule --global --target-http-proxy http-lb-proxy --ports 80
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-1dbce9c2a67de1f9/global/forwardingRules/http-content-rule].
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute forwrding-rules list
ERROR: (gcloud.compute) Invalid choice: 'forwrding-rules'. Did you mean 'forwarding-rules'?
Usage: gcloud compute [optional flags] <group | command>
  group may be           accelerator-types | addresses | backend-buckets |
                         backend-services | commitments | disk-types | disks |
                         firewall-rules | forwarding-rules | health-checks |
                         http-health-checks | https-health-checks | images |
                         instance-groups | instance-templates | instances |
                         interconnects | machine-types | networks | operations |
                         os-login | project-info | regions | routers | routes |
                         shared-vpc | snapshots | ssl-certificates |
                         target-http-proxies | target-https-proxies |
                         target-instances | target-pools | target-ssl-proxies |
                         target-tcp-proxies | target-vpn-gateways | url-maps |
                         vpn-tunnels | xpn | zones
  command may be         config-ssh | connect-to-serial-port | copy-files |
                         reset-windows-password | scp | ssh

For detailed information on this command and its flags, run:
  gcloud compute --help
google308597_student@qwiklabs-gcp-1dbce9c2a67de1f9:~$ gcloud compute forwarding-rules list
NAME               REGION       IP_ADDRESS      IP_PROTOCOL  TARGET
http-content-rule               35.201.108.101  TCP          http-lb-proxy
nginx-lb           us-central1  35.202.93.19    TCP          us-central1/targetPools/nginx-pool


 
