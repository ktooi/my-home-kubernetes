1.  kubeadm のクラスタからノードを削除する。

    Master Node 上で下記のコマンドを実行する。

    ```shell-session
    read -p "Please type the node name to be rename: " __org_node_name
    kubectl drain "${__org_node_name}"
    # kubectl drain "${__org_node_name}" --ignore-daemonsets --delete-emptydir-data
    kubectl delete node "${__org_node_name}"
    ```
2.  Worker Node をリセットする。

    Worker Node 上で下記のコマンドを実行する。

    ```shell-session
    sudo kubeadm reset
    ```

    ```
    [reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
    [reset] Are you sure you want to proceed? [y/N]: y
    ...snip...
    [reset] Deleting contents of config directories: [/etc/kubernetes/manifests /etc/kubernetes/pki]
    [reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/    controller-manager.conf /etc/kubernetes/scheduler.conf]
    [reset] Deleting contents of stateful directories: [/var/lib/kubelet /var/lib/dockershim /var/run/kubernetes /var/lib/cni]
    
    The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d
    
    The reset process does not reset or clean up iptables rules or IPVS tables.
    If you wish to reset iptables, you must do so manually by using the "iptables" command.
    
    If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
    to reset your system's IPVS tables.
    
    The reset process does not clean your kubeconfig files and you must remove them manually.
    Please, check the contents of the $HOME/.kube/config file.
    ```

    ```shell-session
    sudo systemctl stop crio
    sudo mv /etc/cni/net.d{,.bak}  # sudo rm -rf /etc/cni/net.d/
    sudo systemctl start crio
    ```