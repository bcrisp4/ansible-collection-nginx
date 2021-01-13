# bcrisp4.nginx.install

Install Nginx.

## Variables

| Variable | Default | Description |
|-|-|-|
| nginx_package_name | "nginx" | The name of the Nginx package.  |
| nginx_package_version | "" | An optional package version. |

## Examples
```yaml
- hosts: 127.0.0.1
  tasks:
    - import_role:
        name: bcrisp4.nginx.install
```

## Author Information

Ben Crisp <ben@thecrisp.io>

## License

This role is released under the [MIT license](https://github.com/bcrisp4/ansible-collection-nginx/blob/main/LICENSE.txt)
