1.  git clone

    ```shell-session
    git clone https://github.com/jitsi/docker-jitsi-meet.git
    cd docker-jitsi-meet/examples/kubernetes/
    ```
2.  パスワードを生成。

    ```shell-session
    __component_secret=$(< /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c32)
    __jiconfo_auth_password=$(< /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c12)
    __jvb_auth_password=$(< /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c12)
    ```

    ```shell-session
    echo ${__jiconfo_auth_password}
    echo ${__jvb_auth_password}
    ```
3.  namespace 作成。

    ```shell-session
    kubectl create namespace jitsi
    ```
4.  シークレット情報を設定。

    ```shell-session
    kubectl create secret generic jitsi-config -n jitsi --from-literal=JICOFO_COMPONENT_SECRET=${__component_secret} --from-literal=JICOFO_AUTH_PASSWORD=${__jiconfo_auth_password} --from-literal=JVB_AUTH_PASSWORD=${__jvb_auth_password}
    ```
5.  なんやかんや

    ```shell-session
    kubectl create -f jvb-service.yaml
    kubectl create -f rbac.yaml
    kubectl create -f deployment.yaml
    kubectl create -f web-service.yaml
    ```

See also: https://github.com/jitsi/docker-jitsi-meet/blob/master/examples/kubernetes/README.md