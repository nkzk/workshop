# Workshop

Example repository demonstrating gitops with Kustomize and ArgoCD.


## 1 - Fork this repository

## 2 - Change namespace infrastructure-files

Change the following `kustomization` files:

```bash
infra/kustomize/base/kustomization.yaml
infra/kustomize/overlays/dev/kustomization.yaml
infra/kustomize/overlays/test/kustomization.yaml
infra/kustomize/overlays/prod/kustomization.yaml
```

Change `namespace` from "example" to something unique in all mentioned overlays.

Important: Keep the `-dev`/`-test`/`-prod` suffixes.

## 3 - Change name of Argo-resources in `argo/infra`

Change `.metadata.name` in the following files to something of your choosing to avoid name-collisions:

```
./argo/infra/application-set.yaml
./argo/infra/example-application.yaml
./argo/bootstrap-application.yaml
```

### 3.5 - Push the changes to your git repository

Push the changes in step 2 and 3 to your forked git repository.

## 4 - Create a new Argo Application:

Argo -> New app ->

```yaml
General:
  Application Name: Up to you
  Project Name: workshop

Source:
  Repository URL: Your forked project # HTTPS Git URL (.git at the end)
  Path: infra/kustomize/overlays/test

Destination:
  Cluster URL: https://kubernetes.default.svc # Same cluster as Argo.
```


or

`oc apply -f argo/infra/example-application.yaml`

## 5 - Create a new ApplicationSet

`oc apply -f argo/infra/application-set.yaml`

or use OpenShift GUI (+)-button


## 6 - Delete your application and applicationset

Delete your application and applicationset in Argo

## 7 - Create a bootstrap application to create an Application and ApplicationSet the Gitops way.

Create `./argo/bootstrap-application.yaml` in OpenShift Console ( +-button), or in the terminal: `oc apply -f ./argo/bootstrap-application.yaml`

## 8 - Bonus: Create an OpenShift Route to expose web application in "./infra/kustomize/overlays/prod"

Example: (Modify name, namespace ('prod'), and host)

```
kind: Route
apiVersion: route.openshift.io/v1
metadata:
  name: example-route
  namespace: example-prod
spec:
  host: example.com
  to:
    kind: Service
    name: example
    weight: 100
  port:
    targetPort: http
  tls:
    termination: edge
  wildcardPolicy: None
```

## Delete your bootstrap application

Delete your bootstrap application in ArgoCD ('Delete')






### References/Notes

ArgoCD: https://argo-cd.readthedocs.io/en/stable/
ArgoCD + Kustomize: https://argo-cd.readthedocs.io/en/stable/user-guide/kustomize/
ArgoCD ApplicationSets: https://argo-cd.readthedocs.io/en/stable/user-guide/application-set/

OpenShift: https://docs.openshift.com/
Kubernetes: https://kubernetes.io/docs/home/

Main example image used: https://github.com/nkzk/go-stdout
Example web application: https://github.com/nkzk/example-go-image