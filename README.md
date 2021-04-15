# HarnessKustomizePlugin

Purpose of this plugin is as an "Example" to show how harness can inject variables into Kustomize manifests .
This particular example replaces the image in the manifest and makes a commit back to the orginal kustomize GIT versioned deployment manifest to maintain a record.

Ultimately you could use any available commands and utilities in the plugin framework to achieve the same outcome .
For instance you could use go templating 

I have used YQ and bash in this example to rapid prototype .

Here are some more examples in python from other folks 
https://github.com/Agilicus/kustomize-plugins/tree/master/agilicus/v1/imagetransformer

And plugin development documentation 
https://www.bookstack.cn/read/kubernetes-kubectl-en/746cbd49e2286776.md

# General pre-requsites

- Working delegate and kubernetes cluster
- GIT installed on the delegate (this can be done by a delegate profile)
- YQ installed on the delegate (this can be done by a delegate profile)

# GIT SSH and GIT HTTPS pre-requsites

If using GIT ssh , ssh public key should be available and working on the delegate home user (delegate profiles are good for this)
If using HTTPS , you will need your GIT password url encoded and stored in a harness secret called "git_password_url_encoded"

# Files in this repo 

- deployment.yaml and service.yaml (example files for kustomization - nginx deploy)
- kustomization.yaml (standard kustomize file)
- HarnessKustomizePlugin.yaml (Plugin configuration)
- HarnessKustomizePluginScript-GIT-HTTPS (HTTPS Plugin script for addition to your workflow)
- HarnessKustomizePluginScript-GIT-SSH (SSH Plugin script for addition to your workflow)


# Harness configuration (Delegate)

Directory with the following path /root/K_PLUGINS/kustomize/plugin/version1/harnesskustomizeplugin must exist the workflow will create a plugin script here called HarnessKustomizePlugin


# Harness configuration (Service)


![image](https://user-images.githubusercontent.com/44827446/114340816-bdb62880-9b9b-11eb-8a74-d08aff6ff3a0.png)


# Harness configuration (Workflow)

![image](https://user-images.githubusercontent.com/44827446/114340868-d7577000-9b9b-11eb-93ad-7e115e2932f2.png)

![image](https://user-images.githubusercontent.com/44827446/114344651-8fd4e200-9ba3-11eb-9e52-6125785c223f.png)

# How to implement 

1. Setup and test delegate pre-requsites (SSH key , YQ , GIT)

2. Setup your kustomize repo , test and deploy as per documentation 
   https://docs.harness.io/article/zrz7nstjha-use-kustomize-for-kubernetes-deployments
   
3. Add the plugin config yaml to the root of your kustomize repo HarnessKustomizePlugin.yaml

4. Reference it in your kustomization.yaml (see kustomization.yaml example in this repo)
   
5. Configure the plugin directory as per documentation and delegate path above
   https://docs.harness.io/article/zrz7nstjha-use-kustomize-for-kubernetes-deployments
     
6. Add a shell script step in your workflow before the rollout step , name it Inject Harness Plugin , add the contents of HarnessKustomizePluginScript-GIT-HTTPS-AUTH or HarnessKustomizePluginScript-GIT-SSH-AUTH depending on your auth scheme .

Shell Script variable values required :

```
SSH Script

GIT_USER="joebloggs" 
GIT_EMAIL="joebloggs@hotmail.com"
GIT_BRANCH="main"
#reference to harness image artifact variable
IMAGE=${artifact.metadata.image}
#repo URL suffix 
GIT_REPOSITORY_NAME=HarnessKustomizePlugin
#generic repo path and auth 
GIT_REPO_PATH=git@github.com:$GIT_USER/$GIT_REPOSITORY_NAME.git

HTTPS Script

GIT_USER="joebloggs"
GIT_EMAIL="joebloggs@hotmail.com"
#note HTTPS url requires URL encoding for special characters 
GIT_PASSWORD_URLENCODED=${secrets.getValue("git_password_url_encoded")}
GIT_BRANCH="main"
#reference to harness image artifact variable
IMAGE=${artifact.metadata.image}
#repo URL suffix 
REPOSITORY_NAME=HarnessKustomizePlugin
#generic repo path
REPO_PATH=https://github.com/gregkroon/HarnessKustomizePlugin
#build full auth string with URL encoded password for remote repo add and git push 
REPO_AUTH_STRING=https://$GIT_USER:$GIT_PASSWORD_URLENCODED@github.com/$GIT_USER/$REPOSITORY_NAME.git
```


TODO: If your deployment.yaml and kustomize.yaml are in different paths from the root of the repo you will need to adjust the script , future versions will accomodate this .

7. If using SSH you will require your ssh key added and tested manually to the delegate , if using HTTPS you will need to create a secret with the GIT user password that is url encoded called "git_password_url_encoded"

8. Execute the workflow and verify no errors in the "Rollout Deployment step" -> "Intialize logs" and that there is a successfull git commit on the deployment.yaml

# Troubleshooting
