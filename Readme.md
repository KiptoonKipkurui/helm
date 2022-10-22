## HELM

Helm is the package manager for Kubernetes,
Equvalent to homebrew, apt, chocolatey
Helm lets you package and deploy complete applications in Kubernetes.

## Background

Helm provides you the ability to install applications with a single command. A chart can contain other charts as dependencies. You can consequently deploy an entire stack with Helm. You can use Helm like docker-compose but for Kubernetes.
A chart includes templates for various Kubernetes resources to form a complete application. This reduces the microservices complexity and simplifies their management in Kubernetes.
Charts can be compressed and sent to a distant repository. This creates an application artifact for Kubernetes. You can also fetch and deploy existing Helm charts from repositories. This is a strong point for reusability and sharing.
Helm maintains a history of deployed release versions in the Helm workspace. When something goes wrong, rolling back to a previous version is simply — canary release is facilitated with Helm for zero-downtime deployments.
Helm makes the deployment highly configurable. Applications can be customized on the fly during the deployment. By changing parameters, you can use the same chart for multiple environments such as dev, staging, and production.
Streamline CI/CD pipelines – Forward GitOps best practices.

## Installation

```bash

for mac brew install helm

for windows choco install kubernetes-helm

```

[More on installation instructions](https://helm.sh/docs/intro/install/)

## PROBLEM

Sample NGINX deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.21.6
          ports:
            - containerPort: 80
```

Service.yaml to expose NGINX

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

### ISSUES

- Specific values in YAML manifests are hardcoded and not reusable.
- Redundant information to specify such as labels and selectors leads to potential errors.
- Kubectl does not handle potential errors after execution. You've to deploy each file one after the other.
- There's no change traceability.

## Creating Chart

```bash
helm create nginx
```

![alt text for screen readers](/images/tree.png "Text to show on mouseover")

- Chart.yaml: A YAML file containing information about the chart.
  charts: A directory containing any charts upon which this chart depends on.
- templates: this is where Helm finds the YAML definitions for your Services, Deployments, and other Kubernetes objects. You can add or replace the generated YAML files for your own.
- templates/NOTES.txt: This is a templated, plaintext file that gets printed out after the chart is successfully deployed. This is a useful place to briefly describe the next steps for using the chart.
- templates/\_helpers.tpl: That file is the default location for template partials. Files whose name begins with an underscore are assumed to not have a manifest inside. These files are not rendered to Kubernetes object definitions but are available everywhere within other chart templates for use.
- templates/tests: tests that validate that your chart works as expected when it is installed
  values.yaml: The default configuration values for this chart

### Installing

perform a dry run

```bash
helm install nginx nginx

helm install name chart
```

List all deployments

```bash
helm list

```

## Upgrade the release

Imagine you want to upgrade the container image to 1.21.6 for testing purposes.

Instead of creating a new values.yaml, we'll change the setting from the command line.

```bash
helm upgrade nginx nginx --set image.tag=1.21.6
```

check to ensure it has updated

```bash
 kubectl get pod -l app.kubernetes.io/name=nginx -o jsonpath='{.items[0].spec.containers[0].image}'
```

## View chart history

```bash
helm history nginx
```

## Rollback The Helm Release

The upgrade was not conclusive and you want to go back. As Helm keeps all the changes:

```bash

helm rollback nginx 1
```

```bash
kubectl get pod -l app.kubernetes.io/name=nginx -o jsonpath='{.items[0].spec.containers[0].image}'
```

## Uninstall the chart

```bash
helm uninstall nginx
```

## Other charts

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-release bitnami/mysql
```

## References

Other tutorials

- [muffin87](https://github.com/muffin87/helm-tutorial)
- [getbetterdevops](https://getbetterdevops.io/helm-quickstart-tutorial/)
