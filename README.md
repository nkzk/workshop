# Workshop

Example repository demonstrating gitops with Kustomize and ArgoCD.


## 1 - Fork :fork_and_knife:

Fork this repository.

## 2 - Change namespace infrastructure-files :pencil:

Change the following `kustomization` files:

```bash
infra/kustomize/base/kustomization.yaml
infra/kustomize/overlays/dev/kustomization.yaml
infra/kustomize/overlays/test/kustomization.yaml
infra/kustomize/overlays/prod/kustomization.yaml
```

Change `namespace` from "example" to something unique in all mentioned overlays.

Important: Keep the `-dev`/`-test`/`-prod` suffixes.

## 3 - Change name and destination of Argo-resources in `argo/infra` :pencil:

Change `.metadata.name` in the following files to something of your choosing to avoid name-collisions:

```
./argo/infra/application-set.yaml
./argo/infra/example-application.yaml
./argo/bootstrap-application.yaml
```

### Change ApplicationSet :pencil:


To point the ApplicationSet to your git repository, change "repoURL" references in `./argo/infra/application-set.yaml`:

    - Change `.spec.generators[].git.repoURL` to your git url
    - Change `spec.template.spec.source.repoURL` to your git url


Change the generated application template name to avoid namecollisions:

    - Change `.spec.template.metadata.name`


### 3.5 - Push the changes to your git repository :rocket:

Push the changes in step 2 and 3 to your forked git repository.

## 4 - Create a new Argo Application :construction:

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

## 5 - Create a new ApplicationSet :construction:

`oc apply -f argo/infra/application-set.yaml`

or use OpenShift GUI (+)-button


## 6 - Delete your application and applicationset :fire:

Delete your application and applicationset in Argo

## 7 - Create a bootstrap application to create an Application and ApplicationSet the Gitops way :rocket:

Create `./argo/bootstrap-application.yaml` in OpenShift Console ( +-button), or in the terminal: `oc apply -f ./argo/bootstrap-application.yaml`

## 8 - Bonus: Create an OpenShift Route to expose web application in "./infra/kustomize/overlays/prod" :apple:

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

## Delete your bootstrap application :fire:

Delete your bootstrap application in ArgoCD ('Delete')



### References/Notes

ArgoCD: https://argo-cd.readthedocs.io/en/stable/

ArgoCD + Kustomize: https://argo-cd.readthedocs.io/en/stable/user-guide/kustomize/

ArgoCD ApplicationSets: https://argo-cd.readthedocs.io/en/stable/user-guide/application-set/

OpenShift: https://docs.openshift.com/

Kubernetes: https://kubernetes.io/docs/home/

Main example image used: https://github.com/nkzk/go-stdout

Example web application: https://github.com/nkzk/example-go-image