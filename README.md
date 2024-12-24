# K8s Deploy Action

This GitHub Action allows you to deploy YAML configurations to a Kubernetes cluster using `kubectl`. It includes features for configuring the cluster context, updating YAML files dynamically, and managing deployments.

## Inputs

| Input            | Required | Default Value  | Description                                                                                                                          |
| ---------------- | -------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `kube-config`    | Yes      |       | Base64 encoded kubectl configuration for accessing the Kubernetes cluster.                                                           |
| `tag-name`       | Yes      | `staging`      | Docker image tag name to use.                                                                                                        |
| `yaml-file-path` | Yes      |     | Path to the Kubernetes YAML configuration file.                                                                                      |
| `cluster-name`   | No       | `none`         | Name of the Kubernetes cluster to use (context). If not set, the action will use the context currently configured in kube config.    |
| `debug`          | No       | `true`        | Enables or disables debug mode (true/false).                                                                                         |

## Basic usage Example

```yaml
name: Deploy to Kubernetes

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy to Kubernetes
        uses: agro1desenvolvimento/k8s-deploy@v1
        with:
          kube-config: ${{ secrets.KUBE_CONFIG }}
          tag-name: v1.0.0
          yaml-file-path: ./deployments/app.yaml

```

## Creating the kube-config

To create a `kube-config`, follow these steps:

1. **Generate the Configuration File:**

   - Use the following command to fetch the kubeconfig file for your Kubernetes cluster:
     ```bash
        kubectl config view --raw | base64
     ```

3. **Use the Encoded Content:**

   - Copy the Base64 content and create a new action secret in your GitHub repository or organization.


## YAML File Requirements

The Kubernetes YAML file (yaml-file-path) should define the resources you want to deploy. Below is an example structure:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: my-app-namespace
  labels:
    app: my-app
    app.kubernetes.io/name: my-app
  annotations:
    # Optional: Add annotations to track the action in GitHub
    github-action-id: <ACTION-ID>
spec:
  selector:
    matchLabels:
      app: my-app
  replicas: 1
  template:
    metadata:
      labels:
        app: my-app
        app.kubernetes.io/name: my-app
    spec:
      containers:
      - name: my-app
        # Use the tag-name input to set the image tag dynamically here
        image: docker-org/my-app:<ID>
    #continue...
```

## Notes

- Ensure that the `kubectl` configuration has sufficient permissions to manage resources in the cluster.
- The `yaml-file-path` must point to a valid Kubernetes YAML configuration file.


## License

This project is licensed under the [MIT License](LICENSE).

