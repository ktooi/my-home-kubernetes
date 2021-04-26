1.  カーネルモジュール・パラメータ設定。

    ```shell-session
    sudo modprobe br_netfilter

    cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
    br_netfilter
    EOF

    cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    net.ipv4.ip_forward = 1
    EOF
    sudo sysctl --system
    ```
2.  kubeadm インストール。

    ```shell-session
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl
    sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
    echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    sudo apt-get update
    sudo apt-get install -y kubelet kubeadm kubectl
    sudo apt-mark hold kubelet kubeadm kubectl
    ```
3.  Swap 無効化。

    ```shell-session
    sudo swapoff -a
    sudo sed -i 's/^\(.*\tswap\t\)/# \1/' /etc/fstab
    sudo rm /swap.img
    ```
4.  kubeadm 初期化。

    ```shell-session
    sudo kubeadm init --cri-socket "/var/run/crio/crio.sock"
    ```
5.  Metrics Server セットアップ。

    ```shell-session
    kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
    ```
6.  Metrics Server 証明書エラー修正。

    こんな感じでエラーが出ていた。
    ```shell-session
    kubectl -n kube-system logs metrics-server-55b8579d5f-4mdhv
    E0415 15:27:52.846817       1 server.go:132] unable to fully scrape metrics: [unable to fully scrape metrics from node k8s-node01: unable to fetch metrics from node k8s-node01: Get "https://192.168.0.66:10250/stats/summary?only_cpu_and_memory=true": x509: cannot validate certificate for 192.168.0.66 because it doesn't contain any IP SANs, unable to fully scrape metrics from node k8s-master: unable to fetch metrics from node k8s-master: Get "https://192.168.0.67:10250/stats/summary?only_cpu_and_memory=true": x509: cannot validate certificate for 192.168.0.67 because it doesn't contain any IP SANs, unable to fully scrape metrics from node k8s-node02: unable to fetch metrics from node k8s-node02: Get "https://192.168.0.68:10250/stats/summary?only_cpu_and_memory=true": x509: cannot validate certificate for 192.168.0.68 because it doesn't contain any IP SANs]
    ```

    `--kubelet-insecure-tls` 引数を追加。
    ```shell-session
    kubectl edit deploy -n kube-system metrics-server
    ```
    ```
         spec:
           containers:
           - args:
             - --cert-dir=/tmp
             - --secure-port=4443
             - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
             - --kubelet-use-node-status-port
    +        - --kubelet-insecure-tls
             image: k8s.gcr.io/metrics-server/metrics-server:v0.4.2
             imagePullPolicy: IfNotPresent
    ```