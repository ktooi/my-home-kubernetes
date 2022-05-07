Master Node で下記を実行する。

1.  kubeadm 初期化。

    ```shell-session
    $ sudo kubeadm init --cri-socket "/var/run/crio/crio.sock"
    I0507 22:29:53.354182  513023 version.go:255] remote version is much newer: v1.24.0; falling back to: stable-1.23
    [init] Using Kubernetes version: v1.23.6
    [preflight] Running pre-flight checks
    [preflight] Pulling images required for setting up a Kubernetes cluster
    [preflight] This might take a minute or two, depending on the speed of your internet connection
    [preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
    [certs] Using certificateDir folder "/etc/kubernetes/pki"
    [certs] Generating "ca" certificate and key
    [certs] Generating "apiserver" certificate and key
    [certs] apiserver serving cert is signed for DNS names [k8s-master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 172.17.0.16]
    [certs] Generating "apiserver-kubelet-client" certificate and key
    [certs] Generating "front-proxy-ca" certificate and key
    [certs] Generating "front-proxy-client" certificate and key
    [certs] Generating "etcd/ca" certificate and key
    [certs] Generating "etcd/server" certificate and key
    [certs] etcd/server serving cert is signed for DNS names [k8s-master localhost] and IPs [172.17.0.16 127.0.0.1 ::1]
    [certs] Generating "etcd/peer" certificate and key
    [certs] etcd/peer serving cert is signed for DNS names [k8s-master localhost] and IPs [172.17.0.16 127.0.0.1 ::1]
    [certs] Generating "etcd/healthcheck-client" certificate and key
    [certs] Generating "apiserver-etcd-client" certificate and key
    [certs] Generating "sa" key and public key
    [kubeconfig] Using kubeconfig folder "/etc/kubernetes"
    [kubeconfig] Writing "admin.conf" kubeconfig file
    [kubeconfig] Writing "kubelet.conf" kubeconfig file
    [kubeconfig] Writing "controller-manager.conf" kubeconfig file
    [kubeconfig] Writing "scheduler.conf" kubeconfig file
    [kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
    [kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
    [kubelet-start] Starting the kubelet
    [control-plane] Using manifest folder "/etc/kubernetes/manifests"
    [control-plane] Creating static Pod manifest for "kube-apiserver"
    [control-plane] Creating static Pod manifest for "kube-controller-manager"
    [control-plane] Creating static Pod manifest for "kube-scheduler"
    [etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
    [wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
    [apiclient] All control plane components are healthy after 16.006652 seconds
    [upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
    [kubelet] Creating a ConfigMap "kubelet-config-1.23" in namespace kube-system with the configuration for the kubelets in the cluster
    NOTE: The "kubelet-config-1.23" naming of the kubelet ConfigMap is deprecated. Once the UnversionedKubeletConfigMap feature gate graduates to Beta the default name will become just "kubelet-config". Kubeadm upgrade will handle this transition transparently.
    [upload-certs] Skipping phase. Please see --upload-certs
    [mark-control-plane] Marking the node k8s-master as control-plane by adding the labels: [node-role.kubernetes.io/master(deprecated) node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
    [mark-control-plane] Marking the node k8s-master as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
    [bootstrap-token] Using token: r9ywd0.4ygu94wxr37mcghe
    [bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
    [bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
    [bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
    [bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
    [bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
    [bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
    [kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
    [addons] Applied essential addon: CoreDNS
    [addons] Applied essential addon: kube-proxy
    
    Your Kubernetes control-plane has initialized successfully!
    
    To start using your cluster, you need to run the following as a regular user:
    
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config
    
    Alternatively, if you are the root user, you can run:
    
      export KUBECONFIG=/etc/kubernetes/admin.conf
    
    You should now deploy a pod network to the cluster.
    Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
      https://kubernetes.io/docs/concepts/cluster-administration/addons/
    
    Then you can join any number of worker nodes by running the following on each as root:
    
    kubeadm join 172.17.0.16:6443 --token r9ywd0.4ygu94wxr37mcghe \
            --discovery-token-ca-cert-hash sha256:5b1a6b61c2bc550511d402376523589c46c58a26f9f1d8ca7f23103105b507f7 
    ```

    このコマンドの最後に表示される、 `kubeadm join` コマンドは後で Worker Node の構築に利用するのでメモしておく。
2.  kubectl コマンドを使えるようにする。

    ```shell-session
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```
3.  kubectl コマンドを実行して Pod の状態を確認する。

    ```shell-session
    $ kubectl get pods -A
    NAMESPACE     NAME                                 READY   STATUS              RESTARTS   AGE
    kube-system   coredns-64897985d-7mjb8              0/1     ContainerCreating   0          2m32s
    kube-system   coredns-64897985d-s4rcf              0/1     ContainerCreating   0          2m32s
    kube-system   etcd-k8s-master                      1/1     Running             8          2m37s
    kube-system   kube-apiserver-k8s-master            1/1     Running             1          2m37s
    kube-system   kube-controller-manager-k8s-master   1/1     Running             1          2m31s
    kube-system   kube-proxy-5m62h                     1/1     Running             0          2m32s
    kube-system   kube-scheduler-k8s-master            1/1     Running             1          2m36s
    ```

    coredns の Pod が Running になっていないのはこの時点では問題ない。
    [Install Cilium](./install_cilium.md) を実行後、3分くらい待つと coredns の Pod が Running になる。

    ```shell-session
    $ kubectl get pods -A
    NAMESPACE     NAME                                 READY   STATUS    RESTARTS   AGE
    kube-system   cilium-nk2z4                         1/1     Running   0          106s
    kube-system   cilium-operator-dd7c49dd5-ww5w7      1/1     Running   0          106s
    kube-system   coredns-64897985d-7mjb8              1/1     Running   0          9m15s
    kube-system   coredns-64897985d-s4rcf              1/1     Running   0          9m15s
    kube-system   etcd-k8s-master                      1/1     Running   8          9m20s
    kube-system   kube-apiserver-k8s-master            1/1     Running   1          9m20s
    kube-system   kube-controller-manager-k8s-master   1/1     Running   1          9m14s
    kube-system   kube-proxy-5m62h                     1/1     Running   0          9m15s
    kube-system   kube-scheduler-k8s-master            1/1     Running   1          9m19s
    ```
