# Kubernetes クラスターのリセット

Kubernetes クラスターの全ての状態をリセットし、全てのデータを削除します。

## Worker Node を削除する

Master Node で下記を実行します。

現状のクラスターの状態を表示します。

```shell-session
$ kubectl get nodes
NAME           STATUS   ROLES                  AGE   VERSION
k8s-master     Ready    control-plane,master   9h    v1.23.1
k8s-worker01   Ready    <none>                 9h    v1.23.1
k8s-worker02   Ready    <none>                 9h    v1.23.1
k8s-worker03   Ready    <none>                 9h    v1.23.1
```

Worker Node を全て削除します。

```shell-session
$ NODES=($(kubectl get nodes -o jsonpath="{.items[1:].metadata.name}"))
$ echo ${NODES[@]}  # Worker Node が全て表示されることを確認する。
k8s-worker01 k8s-worker02 k8s-worker03
$ kubectl delete node ${NODES[@]}
node "k8s-worker01" deleted
node "k8s-worker02" deleted
node "k8s-worker03" deleted
```

Worker Node 削除後のクラスターの状態を表示します。

```shell-session
$ kubectl get node
NAME         STATUS   ROLES                  AGE   VERSION
k8s-master   Ready    control-plane,master   9h    v1.23.1
```

## Worker Node をリセットする

全ての Worker Node で下記を実行する。

```shell-session
$ sudo kubeadm reset
$ sudo rm -rfv /etc/cni/net.d
```

Ansible で一括実行するならこんな感じ。

```shell-session
$ ansible -i ~/playbooks/inventory/ k8s_worker -b -a 'kubeadm reset -f'
$ ansible -i ~/playbooks/inventory/ k8s_worker -b -a 'rm -rfv /etc/cni/net.d'
```

## Master Node をリセットする

Master Node で下記を実行する。

```shell-session
$ sudo kubeadm reset
[reset] Reading configuration from the cluster...
[reset] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
W0507 16:31:43.156261  481662 utils.go:69] The recommended value for "resolvConf" in "KubeletConfiguration" is: /run/systemd/resolve/resolv.conf; the provided value is: /run/systemd/resolve/resolv.conf
[reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
[reset] Stopping the kubelet service
[reset] Unmounting mounted directories in "/var/lib/kubelet"
[reset] Deleting contents of config directories: [/etc/kubernetes/manifests /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]
[reset] Deleting contents of stateful directories: [/var/lib/etcd /var/lib/kubelet /var/lib/dockershim /var/run/kubernetes /var/lib/cni]

The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.
$ sudo rm -rfv /etc/cni/net.d
```

以上で、クラスターのリセットは完了し、クラスターのすべてのデータは削除されました。
