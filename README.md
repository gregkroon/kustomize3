# HarnessKustomizePlugin

Purpose of this plugin is as an example to show how harness can inject variables into Kustomize manifests .
This particular examples replaces the image in the manifest 

# Pre requsites

# Files in this repo 

- deployment.yaml and service.yaml (example files for kustomization - nginx deploy)
- kustomization.yaml
- HarnessKustomizePlugin.yaml

# Harness configuration (Delegate)

Directory with the following path /root/K_PLUGINS/kustomize/plugin/version1/harnesskustomizeplugin must exist the workflow will create a plugin script here called HarnessKustomizePlugin


# Harness configuration (Service)


![image](https://user-images.githubusercontent.com/44827446/114340816-bdb62880-9b9b-11eb-8a74-d08aff6ff3a0.png)


# Harness configuration (Workflow)

![image](https://user-images.githubusercontent.com/44827446/114340868-d7577000-9b9b-11eb-93ad-7e115e2932f2.png)


```cd /root/K_PLUGINS/kustomize/plugin/version1/harnesskustomizeplugin
rm  HarnessKustomizePlugin

#generate plugin script
cat <<'EOF' >  HarnessKustomizePlugin
#!/bin/sh

#Setup GIT config 
git config --global user.email "git email address"
git config --global user.name "git user"

# capture standad input to file for yq processing 
cat -> input.yaml

# create update for commit to GIT
yq eval '(.spec.template.spec.containers[].image = "${artifact.metadata.image}")'  deployment.yaml > deployment.yaml.new
rm deployment.yaml
mv deployment.yaml.new deployment.yaml

git pull  > /dev/null 2>&1
git add -f deployment.yaml > /dev/null 2>&1
git commit -m "Automated commit"  > /dev/null 2>&1
git remote set-url origin https://username:password@github.com/user/repo.git > /dev/null 2>&1
git push --set-upstream origin main > /dev/null 2>&1

#Process input file and transform for plugin stdout 

yq eval '(.spec.template.spec.containers[].image = "${artifact.metadata.image}")'  input.yaml
EOF

#Set plugin script permissions 
chmod +x HarnessKustomizePlugin
```
