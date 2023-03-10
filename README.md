# DataHub Accelerator

This is an accelerator that can be used to generate a Kubernetes deployment for [DataHub](https://datahubproject.io/).

* Install App Accelerator: (see https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html)
```
tanzu package available list accelerator.apps.tanzu.vmware.com --namespace tap-install
tanzu package install accelerator -p accelerator.apps.tanzu.vmware.com -v 1.0.1 -n tap-install -f resources/app-accelerator-values.yaml
Verify that package is running: tanzu package installed get accelerator -n tap-install
Get the IP address for the App Accelerator API: kubectl get service -n accelerator-system
```

Publish Accelerators:
```
tanzu plugin install --local <path-to-tanzu-cli> all
tanzu acc create datahub --git-repository https://github.com/agapebondservant/datahub-accelerator.git --git-branch main
```

## Contents
1. [Install Datahub via TAP/tanzu cli](#tanzu)
2. [Install Datahub with vanilla Kubernetes](#k8s)

### Install Datahub via TAP/tanzu cli<a name="tanzu"/>

#### Before you begin (one time setup):
1. Create an environment file `.env` (use `.env-sample` as a template), then run:
```
source .env
```

### Install Datahub via vanilla Kubernetes<a name="k8s"/>

#### Before you begin:
