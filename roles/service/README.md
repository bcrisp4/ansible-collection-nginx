# bcrisp4.nginx.service

Manage the Nginx system service.

## Variables

| Variable | Default | Description |
|-|-|-|
| nginx_service_name | "nginx" | The name of the Nginx System service. |
| nginx_service_state | "started" | The desired state of the Nginx systemd service. |
| nginx_service_enabled | true | Should the Nginx system service be enabled? |

## Examples
```yaml
- hosts: 127.0.0.1
  tasks:
    - import_role:
        name: bcrisp4.nginx.service

```

## Author Information

Ben Crisp <ben@thecrisp.io>

## License

This role is released under the [MIT license](https://github.com/bcrisp4/ansible-collection-nginx/blob/main/LICENSE.txt)
