# Workshop

Example repository demonstrating gitops with Kustomize and ArgoCD.


## 1 - Fork this repository

## 2 - Change namespace in `infra/kustomize/*`

Change `namespace: ` to something unique in `kustomization.yaml` in all overlays. Keep the `-dev`/`-test`/`-prod` suffixes.


```
infra/kustomize/base/kustomization.yaml
infra/kustomize/overlays/dev/kustomization.yaml
infra/kustomize/overlays/test/kustomization.yaml
infra/kustomize/overlays/prod/kustomization.yaml
```


## 3 - Change name of application and application-set in `argo/infra`

Change `.metadata.name` in the following files to something of your choosing:

```
./argo/infra/application-set.yaml
./argo/infra/example-application.yaml
./argo/bootstrap-application.yaml
```

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