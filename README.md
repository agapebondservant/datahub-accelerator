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
1. Create an environment file `.env` (use `.env-sample` as a template), then run:
```
source .env
```

#### How to deploy:
1. Add the helm repository:
2. Update the **resources/datahub-prerequisites/values.yaml** file where appropriate.
3. Create a namespace for DataHub:
```
kubectl create ns $DATAHUB_NAMESPACE
```

4. Create secrets for the neo4j and mysql dependencies:
```
kubectl create secret generic mysql-secrets --from-literal=mysql-root-password=$MYSQL_ROOT_PW -n $DATAHUB_NAMESPACE
kubectl create secret generic neo4j-secrets --from-literal=neo4j-password=$NEO4J_PW -n $DATAHUB_NAMESPACE
```

5. Deploy the DataHub pre-requisites (update **resources/charts/prerequisites/values.yaml** as appropriate):
```
helm install prerequisites datahub/datahub-prerequisites -n $DATAHUB_NAMESPACE -f resources/charts/prerequisites/values.yaml
```

6. Verify that the deployment was successful:
```
watch kubectl get pods -n $DATAHUB_NAMESPACE
```

7. Deploy DataHub (update **resources/charts/datahub/values.yaml** as appropriate:)
```
helm install datahub datahub/datahub -n $DATAHUB_NAMESPACE -f resources/charts/datahub/values.yaml
```

8. Deploy the Ingress endpoints:
```
source .env
envsubst < resources/datahub-httpproxy.in.yaml > resources/datahub-httpproxy.yaml
envsubst < resources/datahub-gms-httpproxy.in.yaml > resources/datahub-gms-httpproxy.yaml
kubectl apply -f resources/datahub-httpproxy.yaml -n $DATAHUB_NAMESPACE
kubectl apply -f resources/datahub-gms-httpproxy.yaml -n $DATAHUB_NAMESPACE
```

9. Verify that the deployment was successful:
```
watch kubectl get pods -n $DATAHUB_NAMESPACE
```

Finally, you should be able to access DataHub at http://datahub-<your namespace>.<your domain address>.

## Integrate with TAP

* Deploy the app (on TAP):
```
tanzu apps workload create datahub-tap -f resources/tapworkloads/workload.yaml --yes
```

* Tail the logs of the main app:
```
tanzu apps workload tail datahub-tap --since 64h
```

* Once deployment succeeds, get the URL for the main app:
```
tanzu apps workload get datahub-tap     #should yield datahub.default.<your-domain>
```

* To delete the app:
```
tanzu apps workload delete datahub-tap --yes
```
