# Firebase + WebRTC Codelab

This is the GitHub repo for the FirebaseRTC codelab. This will teach you how
to use Firebase Cloudstore for signalling in a WebRTC video chat application.

See <http://webrtc.org> for details.

## Firebase local hosting

firebase serve --only hosting

## Add SSL to localhost

mkcert -key-file ./certs/key.pem -cert-file ./certs/cert.pem localhost 127.0.0.1 ::1 192.168.31.168

## Config Firewall

1. Open the Control Panel.
2. Go to System and Security > Windows Defender Firewall.
3. Click on Advanced settings.
4. In the left pane, click on Inbound Rules.
5. Click on New Rule....
6. Select Port and click Next.
7. Select TCP and specify the port number (e.g., 80 for HTTP).
8. Allow the connection and click Next.
9. Choose when the rule applies (Domain, Private, Public) and click Next.
10. Give the rule a name and click Finish.

## Nginx config

```conf

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       443 ssl http2;
        server_name  192.168.31.168;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        ssl_certificate C:\Users\Trang\Documents\FirebaseRTC\certs\cert.pem;
        ssl_certificate_key C:\Users\Trang\Documents\FirebaseRTC\certs\key.pem;

        location / {
            proxy_pass http://localhost:5000; # Assuming your Next.js server runs on port 3000
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

