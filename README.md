
alias k="kubectl"

## Create cluster 
```bash
eksctl create cluster -f cluster.yaml --without-nodegroup
kubectl delete daemonset -n kube-system aws-node
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.1/manifests/tigera-operator.yaml
kubectl create -f - <<EOF
kind: Installation
apiVersion: operator.tigera.io/v1
metadata:
  name: default
spec:
  kubernetesProvider: EKS
  cni:
    type: Calico
  calicoNetwork:
    bgp: Disabled
EOF

eksctl create nodegroup eks-otterize-ng \
  --cluster eks-otterize \
  --node-type m5.large \
  --nodes 1 \
  --nodes-min 0 \
  --nodes-max 2 \
  --max-pods-per-node 100 
```

## Install otterize in our cluster, you can also use cloud environment
```bash
helm repo add otterize https://helm.otterize.com
helm repo update
helm install otterize otterize/otterize-kubernetes -n otterize-system --create-namespace
```

## Install otterize cli
```bash
brew install otterize/otterize/otterize-cli
```

### Install demo app
```bash
kubectl apply -n otterize-tutorial-mapper -f https://docs.otterize.com/code-examples/network-mapper/all.yaml
```

### Obtain map from network
```bash
otterize network-mapper export -n otterize-tutorial-mapper --format json
otterize network-mapper visualize -n otterize-tutorial-mapper -o otterize-tutorial-map.png
```

### Delete demo app
```bash
kubectl delete namespace otterize-tutorial-mapper
```

## clean resources
```bash
eksctl delete cluster --name eks-otterize
```

### References
https://docs.otterize.com/quick-tutorials/k8s-network-mapper


