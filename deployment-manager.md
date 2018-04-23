Premade deployment-manager scripts
===

chmod +x proto2json

Use https://www.json2yaml.com/ to see YAML of resource we want.

> Use proto2json to convert official Google document descritpion of REST resource to YAML format.

# An appserver 

## Target

```
        resources:
        - name: appserver
        type: compute.v1.instance 
        properties:
            zone: string
            machineType: string
            networkInterfaces:
            - network: string
            accessConfigs:
            - name: string
                type: string
            disks:
            - type: string
            deviceName: string
            boot: boolean
            autoDelete: boolean
            initializeParams:
                sourceImage: string
```

## Assignment

> Use shell-commands below in cloud-shell get hint about each properties.

```
resources:
- name: appserver
  type: compute.v1.instance 
  properties:
    zone: europe-west1-b
    machineType: https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-afb26840f66377bb/zones/europe-west1-b/machineTypes/f1-micro
    networkInterfaces:
    - network: https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-afb26840f66377bb/global/networks/default
      accessConfigs:
      - name: External_NAT
        type: ONE_TO_ONE_NAT
    disks:
    - type: PERSISTENT
      deviceName: boot
      boot: true
      autoDelete: true
      initializeParams:
        sourceImage: https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/debian-9-stretch-v20180401
```

## Shell command 

- ```gcloud compute zones list```
- ```gcloud compute machine-types list | grep [YOUR_ZONE]```
    - ```gcloud compute machine-types describe f1-micro --zone [YOUR_ZONE]```
        - i.e: ```gcloud compute machine-types describe f1-micro --zone us-west1-b```
            - Use ```selfLink: https://www.googleapis.com/compute/v1/projects/architectingdp/zones/us-west1-b/machineTypes/f1-micro```

- ```gcloud compute networks list```
    - ```
                NAME     MODE  IPV4_RANGE  GATEWAY_IPV4
        default  auto
        ``` 
    - ```gcloud compute networks describe default```
        - Use ```selfLink: https://www.googleapis.com/compute/v1/projects/architectingdp/global/networks/default```

- ```gcloud compute images list```
    - ```gcloud compute images list | grep debian```
        - ```gcloud compute images list --uri | grep debian```
            - ```
                https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/debian-8-jessie-v20170816
                https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/debian-9-stretch-v20170816
                ```
            
## Deployment

> Create appserver environments for four organizations.

```
gcloud deployment-manager deployments create appserver --config appserver.yaml
gcloud deployment-manager deployments create development --config appserver.yaml
gcloud deployment-manager deployments create load-testing --config appserver.yaml
gcloud deployment-manager deployments create security --config appserver.yaml
gcloud deployment-manager deployments create production --config appserver.yaml
```

