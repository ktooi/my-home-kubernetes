1.  certbot インストール

    ```shell-session
    python3 -mvenv certbot
    . certbot/bin/activate
    pip install --upgrade pip
    pip install certbot certbot-dns-route53
    ```