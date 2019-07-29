# Ubuntu v18.04-bionic

### Docker

```
sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io
```

### Nginx - reverse proxy with ssl

```
sudo apt update && sudo apt install nginx

sudo systemctl start nginx && sudo systemctl enable nginx

sudo unlink /etc/nginx/sites-enabled/default

sudo apt-get install software-properties-common

sudo add-apt-repository universe

sudo add-apt-repository ppa:certbot/certbot

sudo apt-get update && sudo apt-get install certbot python-certbot-nginx

sudo certbot certonly --manual -d <DOMAIN_NAME> --preferred-challenges=dns --email devops@fuse.io --register --agree-tos

cd /etc/nginx/sites-available

sudo vim reverse-proxy.conf

server {
  listen 80;
  listen [::]:80;

  access_log /var/log/nginx/reverse-access.log;
  error_log /var/log/nginx/reverse-error.log;

  server_name <DOMAIN_NAME>;

  location / {
      proxy_pass http://127.0.0.1:8545/;
  }

  location /api/health {
      return 200 'OK';
      add_header Content-Type text/plain;
  }
}

server {
  listen 443 ssl ;
  listen [::]:443 ssl ;

  ssl_certificate /etc/letsencrypt/live/<DOMAIN_NAME>/fullchain.pem; # managed by Certbot
  ssl_certificate_key /etc/letsencrypt/live/<DOMAIN_NAME>/privkey.pem; # managed by Certbot
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
  ssl_prefer_server_ciphers on;
  ssl_ciphers HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4;

  access_log /var/log/nginx/reverse-access.log;
  error_log /var/log/nginx/reverse-error.log;

  server_name <DOMAIN_NAME>;

  location / {
    proxy_set_header Host localhost:8545;
    proxy_set_header X-Real-IP $remote_addr;

    proxy_pass http://localhost:8545;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }

  location /api/health {
      return 200 'OK';
      add_header Content-Type text/plain;
  }
}

sudo ln -s /etc/nginx/sites-available/reverse-proxy.conf /etc/nginx/sites-enabled/reverse-proxy.conf

sudo nginx -t

sudo nginx -s reload
```

### Nginx - reverse proxy with websockets and ssl
```
server {
  listen 80;
  listen [::]:80;

  access_log /var/log/nginx/reverse-access.log;
  error_log /var/log/nginx/reverse-error.log;

  server_name <DOMAIN_NAME>;

  location / {
      proxy_pass http://127.0.0.1:8545/;
  }

  location /ws {
     proxy_pass http://127.0.0.1:8546/;
  }

  location /api/health {
      return 200 'OK';
      add_header Content-Type text/plain;
  }
}

server {
  listen 443 ssl ;
  listen [::]:443 ssl ;

  ssl_certificate /etc/letsencrypt/live/<DOMAIN_NAME>/fullchain.pem; # managed by Certbot
  ssl_certificate_key /etc/letsencrypt/live/<DOMAIN_NAME>/privkey.pem; # managed by Certbot
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
  ssl_prefer_server_ciphers on;
  ssl_ciphers HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4;

  access_log /var/log/nginx/reverse-access.log;
  error_log /var/log/nginx/reverse-error.log;

  server_name _;
  client_max_body_size 128k;

  location / {
    proxy_set_header Host localhost:8545;
    proxy_set_header X-Real-IP $remote_addr;

    proxy_pass http://localhost:8545;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }

  location /ws {
    proxy_set_header Host localhost:8546;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Content-Type application/json;

   if ($request_method = 'OPTIONS') {
     add_header Access-Control-Allow-Headers "Origin, X-Requested-With, Content-Type, Accept";
     add_header Access-Control-Allow-Methods "GET, POST, OPTIONS";
     add_header Access-Control-Allow-Origin "*";
     add_header Access-Control-Max-Age 600;
     add_header Content-Type 'text/plain charset=UTF-8';
     add_header Content-Length 0;
     return 204;
    }

    proxy_pass http://localhost:8546;
    proxy_read_timeout 3600s;
    proxy_send_timeout 3600s;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }

  location /api/health {
      return 200 'OK';
      add_header Content-Type text/plain;
  }
}
```

### Create `pfx` from `pem`
```
cd /etc/letsencrypt/live/<domain>

openssl x509 -inform PEM -in cert.pem -outform DER -out <xxx>.cer

openssl pkcs12 -export -out Cert.p12 -in cert.pem -inkey privkey.pem -passin pass:<pass> -passout pass:<pass>

mv Cert.p12 Cert.pxs
```
