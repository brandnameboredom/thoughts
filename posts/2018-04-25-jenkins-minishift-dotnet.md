---
title: Running a dotnet pipeline in jenkins within minishift
description: Running a dotnet pipeline in jenkins within minishift
date: 2018-04-25
tags:
  - Dotnet
  - Minishift
  - Openshift
  - Jenkins
---

I was toying around with the sample [Openshift CI/CD pipeline example] and decided I would try to expand the solution to handle a basic build/test pipeline for a dotnet project I had within minishift.  First things first, I created a new project within my gitlab instance to store the dotnet project code I had and pushed the code into the repository.  Then, I searched around the internet to see if RedHat provided a dotnet jenkins slave image I could leverage to build the source.  Turns out, [they do].  Sweet! So, I built the image and tagged it with my minishift internal docker registry ip using:

``` zsh
cd /my/git/clone/location/for/the/dotnet-jenkins-slave/from/RedHat
docker build -t <minishift_registry_ip>:5000/openshift/dotnet-slave .
```
Now that I tagged the image.  I pushed it to the internal docker registry within minishift.  I had to make sure the account I used to log into the registry had the proper permissions to push images to the openshift image stream.

``` zsh
eval $(minishift docker-env)
docker login <minishift_registry_ip>:5000
docker push <minishift_registry_ip>:5000/openshift/dotnet-slave
```

Ok, now that I have the image in the minishift docker registry, I need to alter the Jenkins image used in the [Openshift CI/CD pipeline example] to include a container definition for the dotnet slave.  I could do this after deploying the Jenkins image but I wanted to play with the packaging as I'm new to Jenkins in general.  So, I cloned [the repo] for the Jenkins image and began editing the ```kube-slave-common.sh``` file.  

First, I added a few variables to hold the dotnet image information.

``` zsh
NODEJS_SLAVE=${NODEJS_SLAVE_IMAGE:-registry.access.redhat.com/openshift3/jenkins-slave-nodejs-rhel7:${JENKINS_SLAVE_IMAGE_TAG}}
MAVEN_SLAVE=${MAVEN_SLAVE_IMAGE:-registry.access.redhat.com/openshift3/jenkins-slave-maven-rhel7:${JENKINS_SLAVE_IMAGE_TAG}}
DOTNET_SLAVE=${DOTNET_SLAVE_IMAGE:-registry.access.redhat.com/dotnet/dotnet-20-jenkins-slave-rhel7:${JENKINS_SLAVE_IMAGE_TAG}}

# if the master is running the centos image, use the centos slave images.
if [[ `grep CentOS /etc/redhat-release` ]]; then
  NODEJS_SLAVE=${NODEJS_SLAVE_IMAGE:-openshift/jenkins-slave-nodejs-centos7:${JENKINS_SLAVE_IMAGE_TAG}}
  MAVEN_SLAVE=${MAVEN_SLAVE_IMAGE:-openshift/jenkins-slave-maven-centos7:${JENKINS_SLAVE_IMAGE_TAG}}
  DOTNET_SLAVE=${DOTNET_SLAVE_IMAGE:-openshift/dotnet-20-jenkins-slave-centos7:${JENKINS_SLAVE_IMAGE_TAG}}
fi
```

Then I copied and pasted the container definition for nodejs and updated the variables and name/label values to the DOTNET_SLAVE variable I added and "dotnet" respectively.  Once this was done, I needed to rebuild the Jenkins image and tag/push to the minishift docker registry in the same way as we did for the slave image.

``` zsh
cd /location/of/Jenkins/git/repo/clone/2
docker build -t <minishift_registry_ip>:5000/openshift/jenkins-dotnet .
docker push <minishift_registry_ip>:5000/openshift/jenkins-dotnet
```

Now, we need to grab the Jenkins openshift template from the Openshift directory in the cloned repo and deploy that to minishift.  During the install wizard, be sure to specify "jenkins-dotnet:latest" as the image.  Once Jenkins is up, login and add the pipeline.  Here was the pipeline I used:

```json
pipeline {
    agent {
      label 'dotnet'
    }
    stages {
      stage('Build') {
        steps {
          git url: 'http://<gitlab_git_url>'
          sh "dotnet build"
        }
      }
      stage('Test') {
        steps {
          sh "dotnet test"
        }
      }
    }
}
```
Now, initiate the pipeline in Jenkins and monitor the "pods" tab within minishift.  You should see it spin up the dotnet container you added to run the pipeline.  You can also watch the output for the pipeline within Jenkins and see the build go through.  Now to add the rest of the CI/CD pipeline...

[Openshift CI/CD pipeline example]: https://github.com/OpenShiftDemos/openshift-cd-demo
[they do]: https://github.com/redhat-developer/dotnet-jenkins-slave
[the repo]: https://github.com/openshift/jenkins
