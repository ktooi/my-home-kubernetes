# Install ArgoCD

ArgoCD を Kubernetes にインストールします。

```shell-session
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

ArgoCD CLI をインストールします。

```shell-session
VERSION=v2.2.5 # Select desired TAG from https://github.com/argoproj/argo-cd/releases
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

ArgoCD にアクセスできるように、 LoadBalancer に変更します。

```shell-session
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

ArgoCD の EXTERNAL-IP を確認します。

```shell-session
$ kubectl get svc argocd-server -n argocd
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                      AGE
argocd-server   LoadBalancer   10.106.105.3   192.168.0.18   80:31619/TCP,443:31776/TCP   11m
```

ArgoCD のパスワードを確認します。

```shell-session
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

ArgoCD にログインします。

```shell-session
$ argocd login 192.168.0.18
WARNING: server certificate had error: x509: cannot validate certificate for 192.168.0.18 because it doesn't contain any IP SANs. Proceed insecurely (y/n)? y
Username: admin
Password: 
'admin:login' logged in successfully
Context '192.168.0.18' updated
```

* [Getting Started - Argo CD - Declarative GitOps CD for Kubernetes](https://argo-cd.readthedocs.io/en/stable/getting_started/)
* [Installation - Argo CD - Declarative GitOps CD for Kubernetes](https://argo-cd.readthedocs.io/en/stable/cli_installation/)
