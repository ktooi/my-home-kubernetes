# Install kube-prometheus

作成中。

[kube-prometheus](https://github.com/prometheus-operator/kube-prometheus)

```
# Create the namespace and CRDs, and then wait for them to be available before creating the remaining resources
kubectl apply --server-side -f manifests/setup
until kubectl get servicemonitors --all-namespaces ; do date; sleep 1; echo ""; done
kubectl apply -f manifests/
```

```

2022/06/02 現在 [kube-prometheus](https://github.com/prometheus-operator/kube-prometheus) の README.md を読んでも Kubernetes 1.23 までしか対応していないように見受けられる。

Grafana で Dashboard を作成しても大半が No data となる。
