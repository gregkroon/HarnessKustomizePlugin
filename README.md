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

# Pre-requsites

- Working delegate and kubernetes cluster
- GIT installed on the delegate (this can be done by a delegate profile)
- YQ installed on the delegate (this can be done by a delegate profile)

# Files in this repo 

- deployment.yaml and service.yaml (example files for kustomization - nginx deploy)
- kustomization.yaml (standard kustomize file)
- HarnessKustomizePlugin.yaml (Plugin configuration)
- HarnessKustomizePluginScript (Plugin script for addition to your workflow)

# Harness configuration (Delegate)

Directory with the following path /root/K_PLUGINS/kustomize/plugin/version1/harnesskustomizeplugin must exist the workflow will create a plugin script here called HarnessKustomizePlugin


# Harness configuration (Service)


![image](https://user-images.githubusercontent.com/44827446/114340816-bdb62880-9b9b-11eb-8a74-d08aff6ff3a0.png)


# Harness configuration (Workflow)

![image](https://user-images.githubusercontent.com/44827446/114340868-d7577000-9b9b-11eb-93ad-7e115e2932f2.png)

![image](https://user-images.githubusercontent.com/44827446/114344651-8fd4e200-9ba3-11eb-9e52-6125785c223f.png)



# Troubleshooting
