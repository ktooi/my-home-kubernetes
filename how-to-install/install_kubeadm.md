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
2.  Swap 無効化。

    ```shell-session
    sudo swapoff -a
    sudo sed -i 's/^\(.*\tswap\t\)/# \1/' /etc/fstab
    sudo rm /swap.img
    ```
3.  kubeadm 初期化。

    ```shell-session
    sudo kubeadm init --cri-socket "/var/run/crio/crio.sock"
    ```