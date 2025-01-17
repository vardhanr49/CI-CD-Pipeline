#  Setup K8-Cluster using kubeadm [K8 Version-->1.32.0] (1/11/2025)

### 1. Update System Packages [On Master & Worker Node]

```bash
sudo apt-get update
```

### 2. Install Docker[On Master & Worker Node]

```bash
sudo apt install docker.io -y
sudo chmod 666 /var/run/docker.sock
```

### 3. Install Required Dependencies for Kubernetes[On Master & Worker Node]

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
# If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
```

### 4. Add Kubernetes Repository and GPG Key[On Master & Worker Node]

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg # allow unprivileged APT programs to read this keyring
```
```bash
    # This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list   # helps tools such as command-not-found to work correctly
```

### 5. Update Package List[On Master & Worker Node]

```bash
sudo apt-get update
```

### 6. Install Kubernetes Components[On Master & Worker Node]

```bash
sudo apt install -y kubeadm kubelet kubectl
```
                  OR
``` bash
sudo apt install -y kubeadm=1.32.0 kubelet=1.32.0 kubectl=1.32.0
```

### 7. Initialize Kubernetes Master Node [On MasterNode] Don't change CIDR range

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 
```

### 8. Configure Kubernetes Cluster [On MasterNode]

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### 9. Here after executing the above command we will get an output similar to below 
    ## kubeadm join 10.0.2.154:6443 --token g8xkdx.r7jfji7n6jprtrwm \
        --discovery-token-ca-cert-hash sha256:013a0b6d7389a1987d263236a63832ef1cdc2e73fe977bf7d62b3c59544eb205
        so add similar one on the worker-nodes in kubernetes. By this worker nodes will be added to the control node.
        
### 10. Deploy Networking Solution (Calico) [On MasterNode]

```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

### 11. Deploy Ingress Controller (NGINX) [On MasterNode]

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml
```

## Reference: 
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/  #(installing k8s)
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/  #(installing Kubeadm,kubelet,kubectl)
