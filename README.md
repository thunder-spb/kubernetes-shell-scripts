# kubernetes-shell-scripts
Various kubernetes scripts for daily use

# Requirements
- `kubectl`
- `jq` - https://jqlang.org
- `yq` - https://github.com/mikefarah/yq

## kubectl-secret-decode

`kubectl-secret-decode` is a utility script designed to interact with Kubernetes secrets. It allows you to retrieve, decode, and display secret data in a human-readable format. This script is particularly useful for debugging and managing secret data within your Kubernetes cluster.

### Features

- Retrieve and decode secret data from a specified Kubernetes secret.
- Supports filtering secrets based on label selectors.
- Allows selection of a specific data key within the secret to be decoded.
- Option to output the decoded data in JSON or YAML format.
- Can display only the data section of the secret when needed.

### Usage

```bash
kubectl-secret-decode [OPTIONS]

Options:
  -c, --context CONTEXT       Specify the Kubernetes context to use.
  -n, --namespace NAMESPACE   Specify the Kubernetes namespace to use.
  -l, --selector SELECTOR     Label selector to filter secrets.
  -k, --key DATA_KEY          Specific key within the secret data to decode.
  -d, --data-only             Display only the decoded data section.
  -o, --output FORMAT         Output format: json or yaml (default: yaml).
  -h, --help                  Show usage information and exit.
```

### Examples

- Retrieve and decode a specific secret:
  ```bash
  kubectl-secret-decode -c my-context -n my-namespace -l my-label -k my-key
  ```

- Display only the data section in JSON format:
  ```bash
  kubectl-secret-decode -d -o json
  ```

This script simplifies the process of accessing and managing secret data, providing clear and flexible options to suit various use cases.

