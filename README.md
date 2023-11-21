# :construction: Workshop :construction:

Example repository demonstrating gitops with Kustomize and ArgoCD.

## :books: 0 - Glossary

- CRD: Kubernetes Custom Resource Definitions. ArgoCD expands the kubernetes api with new resources, for example: "Application", "ApplicationSet" (CRDs)
- Application:  A group of Kubernetes resources as defined by a manifest. This is a Custom Resource Definition (CRD).
- ApplicationSet: This CRD is used to generate multiple Applications based on different 'generators' - for example directories in a path.

## :fork_and_knife: 1 - Fork 

Fork this repository.

## :pencil: 2 - Change infrastructure-files:

### 2.1

Change `namespace` in the following files:

```bash
infra/kustomize/base/kustomization.yaml
infra/kustomize/overlays/dev/kustomization.yaml
infra/kustomize/overlays/test/kustomization.yaml
infra/kustomize/overlays/prod/kustomization.yaml
```


Change `namespace` in the above files from "example" to something unique to avoid naming-collisions.

Important: Keep the `-dev`/`-test`/`-prod` suffixes.


### 2.2

Update `.metadata.name` in the following files:

```bash
./argo/infra/application-set.yaml
./argo/infra/example-application.yaml
./argo/bootstrap-application.yaml
```

Change the `.metadata.name` in the above files to something unique and of your choosing to avoid name-collisions.


### 2.3

Update `./argo/infra/application-set.yaml`:

To point the `ApplicationSet` to your git repository, change "repoURL" references:

- Change `.spec.generators[].git.repoURL` to your git url
- Change `spec.template.spec.source.repoURL` to your git url


Change the generated application template name to avoid namecollisions:

- Change `.spec.template.metadata.name`


## :rocket: 3 - Push the changes to your git repository 

Push the changes in step 2 and 3 to your forked git repository.

## :construction: 4 - Create a new Argo Application 

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

## :construction: 5 - Create a new ApplicationSet

`oc apply -f argo/infra/application-set.yaml`

or use OpenShift GUI (+)-button


## :fire: 6 - Delete your application and applicationset 

Delete your application and applicationset in Argo

## :rocket: 7 - Create a bootstrap application to create an Application and ApplicationSet the Gitops way 

Create `./argo/bootstrap-application.yaml` in OpenShift Console ( +-button), or in the terminal: `oc apply -f ./argo/bootstrap-application.yaml`

## :apple: 8 - Bonus: Create an OpenShift Route to expose web application in "./infra/kustomize/overlays/prod" 

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

## :fire: Delete your bootstrap application 

Delete your bootstrap application in ArgoCD ('Delete')



### :books: References

ArgoCD: https://argo-cd.readthedocs.io/en/stable/

ArgoCD + Kustomize: https://argo-cd.readthedocs.io/en/stable/user-guide/kustomize/

ArgoCD ApplicationSets: https://argo-cd.readthedocs.io/en/stable/user-guide/application-set/

ArgoCD App of App patter: https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#app-of-apps

OpenShift: https://docs.openshift.com/

Kubernetes: https://kubernetes.io/docs/home/

Main example image used: https://github.com/nkzk/go-stdout

Example web application: https://github.com/nkzk/example-go-image