# Upgrade Metallb

## From v0.9.6 to v0.11.0

```shell-session
# see what changes would be made, returns nonzero returncode if different
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl diff -f - -n kube-system

# actually apply the changes, returns nonzero returncode on errors only
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl apply -f - -n kube-system
```

```shell-session
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/metallb.yaml
```

See also: https://metallb.universe.tf/installation/

The results of these commands are below:

```shell-sesion
$ kubectl get configmap kube-proxy -n kube-system -o yaml | \
> sed -e "s/strictARP: false/strictARP: true/" | \
> kubectl diff -f - -n kube-system
diff -u -N /tmp/LIVE-353760985/v1.ConfigMap.kube-system.kube-proxy /tmp/MERGED-1384999721/v1.ConfigMap.kube-system.kube-proxy
--- /tmp/LIVE-353760985/v1.ConfigMap.kube-system.kube-proxy     2022-01-21 14:02:50.109909102 +0900
+++ /tmp/MERGED-1384999721/v1.ConfigMap.kube-system.kube-proxy  2022-01-21 14:02:50.109909102 +0900
@@ -30,7 +30,7 @@
       excludeCIDRs: null
       minSyncPeriod: 0s
       scheduler: ""
-      strictARP: false
+      strictARP: true
       syncPeriod: 0s
       tcpFinTimeout: 0s
       tcpTimeout: 0s
@@ -79,7 +79,6 @@
     fieldsV1:
       f:data:
         .: {}
-        f:config.conf: {}
         f:kubeconfig.conf: {}
       f:metadata:
         f:annotations:
@@ -91,6 +90,14 @@
     manager: kubeadm
     operation: Update
     time: "2021-04-13T14:14:34Z"
+  - apiVersion: v1
+    fieldsType: FieldsV1
+    fieldsV1:
+      f:data:
+        f:config.conf: {}
+    manager: kubectl-client-side-apply
+    operation: Update
+    time: "2022-01-21T05:02:50Z"
   name: kube-proxy
   namespace: kube-system
   resourceVersion: "245"
```

```shell-session
$ kubectl get configmap kube-proxy -n kube-system -o yaml | \
> sed -e "s/strictARP: false/strictARP: true/" | \
> kubectl apply -f - -n kube-system
Warning: resource configmaps/kube-proxy is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
configmap/kube-proxy configured
```

```shell-session
$ kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/namespace.yaml
namespace/metallb-system unchanged
```

```shell-session
$ kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/metallb.yaml
Warning: policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
podsecuritypolicy.policy/controller configured
podsecuritypolicy.policy/speaker configured
serviceaccount/controller unchanged
serviceaccount/speaker unchanged
clusterrole.rbac.authorization.k8s.io/metallb-system:controller configured
clusterrole.rbac.authorization.k8s.io/metallb-system:speaker configured
role.rbac.authorization.k8s.io/config-watcher unchanged
role.rbac.authorization.k8s.io/pod-lister unchanged
role.rbac.authorization.k8s.io/controller created
clusterrolebinding.rbac.authorization.k8s.io/metallb-system:controller unchanged
clusterrolebinding.rbac.authorization.k8s.io/metallb-system:speaker unchanged
rolebinding.rbac.authorization.k8s.io/config-watcher unchanged
rolebinding.rbac.authorization.k8s.io/pod-lister unchanged
rolebinding.rbac.authorization.k8s.io/controller created
daemonset.apps/speaker configured
deployment.apps/controller configured
```