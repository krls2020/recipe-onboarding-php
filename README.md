# Zerops Hello World For PHP

```yaml
project:
  name: zerops-hello-world-php
  tags:
    - zerops
    - hello-world

services:
  - hostname: phpnginx81
    type: php-nginx@8.1+1.22
    envSecrets:
      DB_HOST: db
      DB_NAME: db
      DB_PASS: ${db_password}
      DB_PORT: "5432"
      DB_USER: ${db_user}
    enableSubdomainAccess: true
    buildFromGit: https://github.com/krls2020/recipe-onboarding-php
    nginxConfig: |
      server {
          listen 80;
          listen [::]:80;

          server_name _;

          # Be sure that you set up a correct document root!
          root /var/www;

          location ~ \.php {
              try_files _ @backend;
          }

          location / {
              # use this for pretty url
              try_files $uri /$uri /index.html /index.php$is_args$args;
          }

          location @backend {
              fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
              fastcgi_split_path_info ^(.+\.php)(/.*)$;
              include fastcgi_params;

              fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
              fastcgi_param DOCUMENT_ROOT $realpath_root;
              internal;
          }

          access_log syslog:server=unix:/dev/log,facility=local1 default_short;
          error_log syslog:server=unix:/dev/log,facility=local1;
      }
    minContainers: 1

  - hostname: phpapache81
    type: php-apache@8.1+2.4
    envSecrets:
      DB_HOST: db
      DB_NAME: db
      DB_PASS: ${db_password}
      DB_PORT: "5432"
      DB_USER: ${db_user}
    enableSubdomainAccess: true
    buildFromGit: https://github.com/krls2020/recipe-onboarding-php
    minContainers: 1

  - hostname: adminer
    type: php-apache@8.0+2.4
    buildFromGit: https://github.com/zeropsio/recipe-adminer@main
    enableSubdomainAccess: true
    minContainers: 1
    maxContainers: 1

  - hostname: db
    type: postgresql@14
    mode: NON_HA
    priority: 1
```

