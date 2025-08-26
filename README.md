## kubernetes-shell-scripts
Various kubernetes scripts for daily use

### Overview

This repository contains a collection of Bash scripts that streamline common Kubernetes tasks using `kubectl`.

### Requirements

- `kubectl`
- `jq`
- `yq`
- `column` and `sed` (usually available on macOS/Linux)
- `fzf` (optional; only if you use interactive mode where noted)

### Scripts

#### kubectl-secret-decode

Decode Kubernetes `Secret` data into human-readable values, or list secrets.

Usage:

```bash
kubectl-secret-decode [secret-name] [OPTIONS]

Options:
  -c, --context CONTEXT       Kubernetes context to use
  -n, --namespace NAMESPACE   Namespace where the secret resides
  -l, --selector SELECTOR     Label selector to filter secrets
  -k, --key DATA_KEY          Specific key in the secret's data to decode (with secret-name)
  -d, --data-only             Output only the data section
  -o, --output FORMAT         Output format: yaml (default) or json
  -h, --help                  Show help and exit
```

Examples:

```bash
# List secrets in a namespace
kubectl-secret-decode -n kube-system

# Decode entire secret as YAML
kubectl-secret-decode my-secret -n default

# Decode a single key and print only data as JSON
kubectl-secret-decode my-secret -n default -k password -d -o json
```

#### kubectl-fancy-get-resource

Get any Kubernetes resource as YAML or JSON, or list resources. Interactive mode via `fzf` is planned (WIP).

Usage:

```bash
./kubectl-fancy-get-resource <resource-type> [resource-name] [OPTIONS]

Options:
  -c, --context CONTEXT       Kubernetes context to use
  -n, --namespace NAMESPACE   Namespace where the resource resides
  -l, --selector SELECTOR     Label selector to filter resources
  -i, --interactive           Interactive mode (WIP)
  -o, --output FORMAT         Output format: yaml (default) or json
  -h, --help                  Show help and exit
  -d, --debug                 Enable debug mode
```

Behavior:
- If `resource-name` is omitted, the script lists resources of the given type via `kubectl get <type>`.
- If `resource-name` is provided, the script fetches the resource and prints it as YAML (default) or JSON.

Examples:

```bash
# List pods in a namespace
./kubectl-fancy-get-resource pods -n default

# Get a deployment as YAML
./kubectl-fancy-get-resource deployment my-app -n prod -o yaml

# Get a service as JSON
./kubectl-fancy-get-resource service my-svc -n default -o json
```

#### kubectl-definalize

Remove finalizers from a resource to unblock deletion.

Usage:

```bash
./kubectl-definalize <resource-type> <resource-name> [OPTIONS]

Options:
  -c, --context CONTEXT       Kubernetes context to use
  -n, --namespace NAMESPACE   Namespace where the resource resides
  -l, --selector SELECTOR     Label selector to filter resources
  -h, --help                  Show help and exit
  -d, --debug                 Enable debug mode
```

Example:

```bash
# Remove finalizers from a stuck namespace-scoped resource
./kubectl-definalize pod my-pod -n default
```

#### kubectl-fancy-nodes

Node insights with two modes: node types/provisioners (default) and allocatable vs running pods.

Usage:

```bash
./kubectl-fancy-nodes [OPTIONS]

Options:
  -p, --pods                  Show node common info about allocatable and running pods.
  -t, --types                 (Default) Show node types and provisioner.
  -c, --context <context>     (Optional) Kubernetes context to use
  -l, --selector <label-selector> (Optional) Label selector to filter resources
  -h, --help                  Display this help message
```

Examples:

```bash
# Show node types/provisioners with status
./kubectl-fancy-nodes --types
```
Example output:
```bash
NAME                                        STATUS  AGE    IP            VERSION              PROVISIONER    INSTANCE_TYPE
----                                        ------  -----  --            -------              -----------    -------------
ip-10-83-248-126.eu-west-2.compute.internal    🟢    4d18h  10.83.248.126  v1.33.0-eks-802817d  Karpenter      c6a.xlarge
ip-10-83-248-234.eu-west-2.compute.internal    🟢    4d18h  10.83.248.234  v1.33.0-eks-802817d  Karpenter      c6a.xlarge
ip-10-83-248-27.eu-west-2.compute.internal     🟢    1d22h  10.83.248.27   v1.33.0-eks-802817d  EKS NodeGroup  c5.2xlarge
ip-10-83-248-4.eu-west-2.compute.internal      🟢    4d18h  10.83.248.4    v1.33.0-eks-802817d  Karpenter      c6a.xlarge
ip-10-83-248-72.eu-west-2.compute.internal     🟢    4d18h  10.83.248.72   v1.33.0-eks-802817d  Karpenter      m6a.xlarge
ip-10-83-248-97.eu-west-2.compute.internal     🟢    4d18h  10.83.248.97   v1.33.0-eks-802817d  Karpenter      c6a.xlarge
ip-10-83-249-137.eu-west-2.compute.internal    🟢    4d18h  10.83.249.137  v1.33.0-eks-802817d  Karpenter      c6a.xlarge
ip-10-83-249-185.eu-west-2.compute.internal    🟢    1d22h  10.83.249.185  v1.33.0-eks-802817d  EKS NodeGroup  c5.2xlarge
ip-10-83-249-212.eu-west-2.compute.internal    🟢    1d22h  10.83.249.212  v1.33.0-eks-802817d  EKS NodeGroup  c5.2xlarge
ip-10-83-249-56.eu-west-2.compute.internal     🟢    4d18h  10.83.249.56   v1.33.0-eks-802817d  Karpenter      c6a.xlarge
```

```bash
# Show allocatable vs running pods per node
./kubectl-fancy-nodes --pods
```

Example output:
```bash
NAME                                        INSTANCE_TYPE  PROVISIONER    TOTAL  RUNNING
----                                        -------------  -----------    -----  -------
ip-10-83-248-126.eu-west-2.compute.internal  c6a.xlarge     Karpenter      58     19
ip-10-83-248-234.eu-west-2.compute.internal  c6a.xlarge     Karpenter      58     18
ip-10-83-248-27.eu-west-2.compute.internal   c5.2xlarge     EKS NodeGroup  58     7
ip-10-83-248-4.eu-west-2.compute.internal    c6a.xlarge     Karpenter      58     30
ip-10-83-248-72.eu-west-2.compute.internal   m6a.xlarge     Karpenter      58     21
ip-10-83-248-97.eu-west-2.compute.internal   c6a.xlarge     Karpenter      58     21
ip-10-83-249-137.eu-west-2.compute.internal  c6a.xlarge     Karpenter      58     17
ip-10-83-249-185.eu-west-2.compute.internal  c5.2xlarge     EKS NodeGroup  58     6
ip-10-83-249-212.eu-west-2.compute.internal  c5.2xlarge     EKS NodeGroup  58     11
ip-10-83-249-56.eu-west-2.compute.internal   c6a.xlarge     Karpenter      58     22
ip-10-83-249-56.eu-west-2.compute.internal   c6a.xlarge     Karpenter      58     22
```
