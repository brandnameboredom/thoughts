---
title: ConfigMap VolumeMount File Overwrite
description: ConfigMap VolumeMount File Overwrite
date: 2018-09-19
tags:
  - Openshift
  - Kubernetes
  - VolumeMount
  - ConfigMap
  - Keycloak
---

# Openshift/Keycloak Config

I was integrating keycloak configuration into my create-react generated app today when I stumbled upon an issue attempting to mount my configmap within my container in Openshift.

I thought I would be able to simply supply the mountpath down to the file I wanted to inject
```
containers:
- volumeMounts:
  - name: cloakconfig
    mountPath: /public/keycloak.json
```
I found, after stumbling upon [this Kubernetes issues], that I needed to specify a subpath of the specific file name I wanted to overwrite:
```
containers:
- volumeMounts:
  - name: cloakconfig
    mountPath: /public/keycloak.json
    subPath: keycloak.json
```

Now, when I deploy the app, the keycloak.json file is properly overwritten with the information I'm providing within my deployment config template.  Nice.

[this Kubernetes issue]: https://github.com/kubernetes/kubernetes/issues/44815#issuecomment-297077509
