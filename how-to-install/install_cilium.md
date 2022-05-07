# Cilium Installation

## with Cilium CLI

```shell-session
$ cilium install
ℹ️  using Cilium version "v1.11.3"
🔮 Auto-detected cluster name: kubernetes
🔮 Auto-detected IPAM mode: cluster-pool
ℹ️  helm template --namespace kube-system cilium cilium/cilium --version 1.11.3 --set cluster.id=0,cluster.name=kubernetes,encryption.nodeEncryption=false,ipam.mode=cluster-pool,kubeProxyReplacement=disabled,operator.replicas=1,serviceAccounts.cilium.name=cilium,serviceAccounts.operator.name=cilium-operator
ℹ️  Storing helm values file in kube-system/cilium-cli-helm-values Secret
🔑 Created CA in secret cilium-ca
🔑 Generating certificates for Hubble...
🚀 Creating Service accounts...
🚀 Creating Cluster roles...
🚀 Creating ConfigMap for Cilium version 1.11.3...
🚀 Creating Agent DaemonSet...
level=warning msg="spec.template.spec.affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms[1].matchExpressions[0].key: beta.kubernetes.io/os is deprecated since v1.14; use \"kubernetes.io/os\" instead" subsys=klog
level=warning msg="spec.template.metadata.annotations[scheduler.alpha.kubernetes.io/critical-pod]: non-functional in v1.16+; use the \"priorityClassName\" field instead" subsys=klog
🚀 Creating Operator Deployment...
⌛ Waiting for Cilium to be installed and ready...
♻️  Restarting unmanaged pods...
♻️  Restarted unmanaged pod kube-system/coredns-64897985d-7zw29
♻️  Restarted unmanaged pod kube-system/coredns-64897985d-rpj5k
✅ Cilium was successfully installed! Run 'cilium status' to view installation health
```

```shell-session
$ cilium status
    /¯¯\
 /¯¯\__/¯¯\    Cilium:         OK
 \__/¯¯\__/    Operator:       OK
 /¯¯\__/¯¯\    Hubble:         disabled
 \__/¯¯\__/    ClusterMesh:    disabled
    \__/

DaemonSet         cilium             Desired: 1, Ready: 1/1, Available: 1/1
Deployment        cilium-operator    Desired: 1, Ready: 1/1, Available: 1/1
Containers:       cilium             Running: 1
                  cilium-operator    Running: 1
Cluster Pods:     2/2 managed by Cilium
Image versions    cilium             quay.io/cilium/cilium:v1.11.3@sha256:cb6aac121e348abd61a692c435a90a6e2ad3f25baa9915346be7b333de8a767f: 1
                  cilium-operator    quay.io/cilium/operator-generic:v1.11.3@sha256:5b81db7a32cb7e2d00bb3cf332277ec2b3be239d9e94a8d979915f4e6648c787: 1
```

## with Manifest

```shell-session
kubectl create -f https://raw.githubusercontent.com/cilium/cilium/v1.9/install/kubernetes/quick-install.yaml
```

## FYI

See also: https://docs.cilium.io/en/v1.9/gettingstarted/k8s-install-default/