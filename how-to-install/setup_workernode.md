Worker Node で `kubeadm join` を実行する。

実行コマンドは Master Node で `kubeadm init` した際に表示されたものをコピペして実行する。

```shell-session
$ sudo kubeadm join 192.0.2.1:6443 --token XXXXX --discovery-token-ca-cert-hash sha256:0000000000000000000000000000000000000000000000000000000000000000
```

なお、 Token の有効期限が切れてしまった場合は、 Master Node で下記コマンドを実行することで再度 Token を取得することができる。

```shell-session
$ kubeadm token create --print-join-command
kubeadm join 192.0.2.1:6443 --token XXXXX --discovery-token-ca-cert-hash sha256:0000000000000000000000000000000000000000000000000000000000000000
```

Ansible で実行する場合は、 Master Node から下記コマンドを実行すればよい。

```shell-session
$ ansible -i ~/playbooks/inventory/ k8s_worker -b -a "$(kubeadm token create --print-join-command)"
$ kubeadm token delete $(kubeadm token list -o jsonpath='{.token} ')
bootstrap token "536h2s" deleted
bootstrap token "g53uwi" deleted
```
