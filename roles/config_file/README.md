# bcrisp4.nginx.config_file

Manage Nginx configuration files

## Variables

| Variable | Default | Description |
|-|-|-|
| nginx_config_file_config | {} | A dictionary describing the desire Nginx configuration parameters. See examples. |
| nginx_config_file_path | "/etc/nginx/nginx.conf" | The path where the Nginx configuration file should be created. |
| nginx_config_file_owner | "root" | The user or UID that should own the Nginx configuration file. |
| nginx_config_file_group | "root" | The group or GID that should own the Nginx configuration file. |
| nginx_config_file_mode | "ugo=r" | The file permissions that should be applied to the Nginx configuration file. |
| nginx_config_file_handlers | [] | An optional list of handlers that should be notified if the Nginx configuration file has changed. |

## Examples
```yaml
- hosts: 127.0.0.1
  vars:
    nginx_config_file_config:
      user: www-data
      worker_processes: auto
      pid: /run/nginx.pid
      http:
        error_log: /var/log/nginx/error.log
        access_log: /var/log/nginx/access.log
        log_format: |-
          main '$remote_addr - $remote_user [$time_local] "$request" '
               '$status $body_bytes_sent "$http_referer" '
               '"$http_user_agent" "$http_x_forwarded_for"'
        events:
          worker_connections: 768
        upstream php-handler:
          server: nix:/run/php/php-fpm.sock
        server:
          -
            server_name: example.org
            listen:
              - 80
              - '[::]:80'
            return: 301 https://$server_name$request_uri
          -
            server_name: example.org
            listen:
              - 443 ssl http2
              - '[::]:443 ssl http2'
            ssl_certificate: /etc/ssl/certs/example.org.pem;
            ssl_certificate_key: /etc/ssl/private/example.org.key;
            root: /var/www/html
            index: index.php index.html /index.php$request_uri
            location /robots.txt:
                allow: all
                log_not_found: false
                access_log: false
            location /:
              try_files: $uri /index.php$request_uri
              location /home:
                return: 301 /
  tasks:
    - import_role:
        name: bcrisp4.nginx.config_file

```
would create `/etc/nginx/conf` like:
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
http {
    error_log /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
         '$status $body_bytes_sent "$http_referer" '
         '"$http_user_agent" "$http_x_forwarded_for"';
    events {
        worker_connections 768;
    }
    upstream php-handler {
        server nix:/run/php/php-fpm.sock;
    }
    server {
        server_name example.org;
        listen 80;
        listen [::]:80;
        return 301 https://$server_name$request_uri;
    }
    server {
        server_name example.org;
        listen 443 ssl http2;
        listen [::]:443 ssl http2;
        ssl_certificate /etc/ssl/certs/example.org.pem;;
        ssl_certificate_key /etc/ssl/private/example.org.key;;
        root /var/www/html;
        index index.php index.html /index.php$request_uri;
        location /robots.txt {
            allow all;
            log_not_found off;
            access_log off;
        }
        location / {
            try_files $uri /index.php$request_uri;
            location /home {
                return 301 /;
            }
        }
    }
}
```

## The Nginx Config Template

### Blocks

Mappings/dicts are transformed into Nginx blocks. You can nest dicts to nest blocks. The macro will recurse through the nested dicts.
```yaml
http:
  server:
    location /: {}
    location /api: {}
```
```
http {
    server {
        location / {
        }
        location /api {
        }
    }
}
```

To define multiple blocks with the same name (e.g multiple `server` blocks), use lists. Like:
```yaml
server:
  -
    parameter: value
  -
    parameter2: value2
```
which becomes:
```
server {
    parameter value;
}
server {
    parameter2 value2;
```

### Lists

A list of parameters (i.e. not dicts) of length `n` is transformed into `n` instances of the parameter. E.g.
```yaml
listen:
  - 80
  - '[::]:80'
```
becomes
```
listen 80;
listen [::]:80;
```
### Booleans

Boolean values are translated to `on` or `off` as appropriate
```yaml
access_log: false
log_not_found: true
```
```
access_log off;
log_not_found on;
```

## Author Information

Ben Crisp <ben@thecrisp.io>

## License

This role is released under the [MIT license](https://github.com/bcrisp4/ansible-collection-nginx/blob/main/LICENSE.txt)
