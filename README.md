# Setup Guide for `argocd-autopilot-demo` in a New Kubernetes Environment

## Prerequisites
- A Kubernetes cluster with at least one control plane and worker nodes set up (e.g., 1 control plane node and 3 worker nodes).
- The `kubectl` CLI installed and configured to interact with the cluster.
- The `argocd-autopilot` CLI installed for bootstrapping ArgoCD.

## Steps

### 1. **Prepare Kubernetes Config (if needed)**
If you encounter a **permission denied** error when using `kubectl`, set up the correct Kubernetes context:
```bash
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
export KUBECONFIG=$HOME/.kube/config
```

### 2. **Verify Cluster Status**
Verify that your cluster nodes are running properly by using `kubectl get nodes`:
```bash
kubectl get nodes
```
This command should show all the nodes in your cluster with their status as `Ready`.

### 3. **Clone the Repository**
Clone the `argocd-autopilot-demo` repository to your local environment:
```bash
git clone https://github.com/yoshiouchi/argocd-autopilot-demo.git
cd argocd-autopilot-demo
```

### 4. **Bootstrap the Repo Using `argocd-autopilot`**
Ensure the `argocd-autopilot` CLI is installed, and bootstrap the repository into your Kubernetes cluster:
```bash
argocd-autopilot repo bootstrap --repo https://github.com/yoshiouchi/argocd-autopilot-demo --context k8s-controlplane
```
During the bootstrap process, you may encounter errors related to existing folders (`bootstrap` or `projects`). If this happens, choose one of the following options:
- Remove the existing folders (`bootstrap` or `projects`) from the repository and push the changes back.
- Specify a new installation path using the `--installation-path` flag:
```bash
argocd-autopilot repo bootstrap --repo https://github.com/yoshiouchi/argocd-autopilot-demo --context k8s-controlplane --installation-path <new-folder>
```

### 5. **Verify ArgoCD Deployment**
After the bootstrap process completes, check the status of ArgoCD components:
```bash
kubectl get pods -n argocd
```
This will confirm that the ArgoCD server and related components are running in your cluster.

### 6. **(Optional) Handle GitHub Authentication**
If prompted for a `git-token`, generate a GitHub Personal Access Token (PAT) with the required permissions (e.g., `repo` scope), and provide it in the command:
```bash
argocd-autopilot repo bootstrap --repo https://github.com/yoshiouchi/argocd-autopilot-demo --context k8s-controlplane --git-token <your-git-token>
```

## **Additional Notes**
- Ensure you have the correct `kubectl` context set for interacting with your cluster.
- If you face any issues with Kubernetes config files or permissions, always double-check your user permissions and context setup.

## **Troubleshooting**
- Permission Denied with `kubectl`: If you see errors like `error loading config file "/etc/kubernetes/admin.conf": permission denied`, it may be due to insufficient permissions. You can resolve it by copying the `admin.conf` to your home directory and changing the ownership:
```bash
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
export KUBECONFIG=$HOME/.kube/config
```
- Folder Already Exists Error: If you encounter the error `FATAL folder bootstrap already exists in: /bootstrap`, it means the bootstrap folder already exists. You can either remove the existing folder or specify a new installation path.
```bash
argocd-autopilot repo bootstrap --repo https://github.com/yoshiouchi/argocd-autopilot-demo --context k8s-controlplane --installation-path <new-folder>
```
- GitHub Token Required: If you are prompted for a GitHub token during the bootstrap process, ensure you have a valid GitHub Personal Access Token (PAT) and provide it via the `--git-token` flag.

## **Conclusion**
Following these steps should allow you to successfully set up the `argocd-autopilot-demo` repository in your new Kubernetes environment and deploy ArgoCD. Make sure your Kubernetes context is correctly set up and your GitHub authentication is handled properly. If you face any issues, refer to the troubleshooting section for potential solutions.
