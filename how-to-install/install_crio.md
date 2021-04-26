1.  環境変数を定義。

    ```shell-session
    OS=xUbuntu_20.04
    VERSION=1.18
    ```
2.  リポジトリを設定。

    ```shell-session
    echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
    echo "deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list
    ```
3.  リポジトリのキーをインポート。

    ```shell-session
    curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/Release.key | sudo apt-key add -
    curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key add -
    ```
4.  cri-o をインストール。

    ```shell-session
    sudo apt-get update
    sudo apt-get install -y cri-o cri-o-runc
    ```
5.  cri-o を起動。

    ```shell-session
    sudo systemctl enable crio
    sudo systemctl start crio
    ```

See also: https://cri-o.io/