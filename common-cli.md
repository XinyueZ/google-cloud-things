Introduce
==== 

List some common CLIs when work with Google Cloud Platform(GCP). Sometimes we can copy command directly instead Tab-Complete(You might know the problems).

# Ready

### Local computer init

```gcloud init```

Use this CLI on local computer to initilize global configuration of GCP on the local computer. You will see clear instructions for use, simple and clear. 

1. Login with Google account
2. Connect to a Cloud project

### Auth 

```gcloud auth list```

List current auth-user and you'll see login email etc.

# Project

### Show project

```gcloud config list project```

List current connected project.

### Set/connect project

```gcloud config set project hello-world-project```

Connect current GCP-Environment to project ```hello-world-project``` .


 
