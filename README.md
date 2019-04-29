# Docker Grafana

>> Grafana running in a docker container

[![Build Status](https://travis-ci.org/ppadial/ansible-role-docker-grafana.svg?branch=master)](https://travis-ci.org/ppadial/ansible-role-docker-grafana)

## Requirements

Requires `docker` to be installed on the server.

## Role Actions (Tags)

This role contains a set of tags that can be used to determine which operations we wanna run.

By default, it does all the operations: configure, install, postconfigure (datasources and dashboards) and install/update plugins. But you can decide which operations you wanna do, as example, imagine that you wanna just uninstall/install a set of plugins. Then you can execute the role using the tag "plugins" and just this operations will be executed.

The tag list:

| Tag | Description |
|---|---|
| preflight | Configure the data directory in the server |
| install | Start the docker container |
| postconfiguration | Install datasources and dashboards specified |
| plugins | Install/Update the plugins specified |



## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml` and `vars/main.yml`).

### Generic related variables

| NAME | DEFAULT | DESCRIPTION   | file  |
|---|---|---|---|
| grafana_data_dir  | /var/grafana/data  | folder to store grafana data files  | defaults/main.yml |
| grafana_admin_username | admin | name of the default admin username when install grafana | defaults/main.yml |
| grafana_admin_password | grafana | pass of the default admin username when install grafana | defaults/main.yml |

### Grafana related variables

The list of environment variables to pass to the grafana container.

```yml
    grafana_environment_vars: []
```

it can contains any of the environment variables available [here](https://hub.docker.com/r/grafana/grafana/).
An example:

```yml
grafana_environment_vars:
  GF_SERVER_ROOT_URL: http://grafana.server.name
```

### Grafana Datasources

This role can create datasources for you as a post-install action. The definition of the datasources is using a variable that contains a dictionary on each line specifing the datasource to create. (Basically is a wrapper of the ansible [grafana_datasource](https://docs.ansible.com/ansible/latest/modules/grafana_datasource_module.html?highlight=grafana_datasource) module, so you can read about different values of the doc)

```yml
    grafana_environment_vars: []
```

the supported variables inside each datasource are:

```yml
 org_id: "{{ item.org | default(1) }}"
 name: "{{ item.name }}"
 access: "{{ item.access | default(proxy) }}"
 ds_type: "{{ item.ds_type }}"
 url: "{{ item.url }}"
 database: "{{ item.database | default(omit) }}"
 time_interval: "{{ item.time_interval | default(omit) }}"
 user: "{{ item.user | default(omit) }}"
 password: "{{ item.password | default(omit) }}"
 sslmode: "{{ item.sslmode | default(omit) }}"
```

Example of a prometheus datasource:

```yml
grafana_datasources:
  - { name: "from_ansible", ds_type: "prometheus", access: "direct", url: "http://prometheus:9090"}
```

### Grafana Dashboards

This role can create dashboards for you as a post-install action. (Basically is a wrapper of the ansible [grafana_dashboard](https://docs.ansible.com/ansible/latest/modules/grafana_dashboard_module.html)).

It takes all files inside `{{ inventory_dir }}/resources/grafana/dashboards/*.json` and load them into the default organisation (1).

You has to select the default datasource if is not included into the dashboard (after install)

### Grafana Plugins (Update/Remove)

This role can install/update plugins for you as a post-install action. (Basically is a wrapper of the ansible [grafana_plugin](https://docs.ansible.com/ansible/latest/modules/grafana_plugin_module.html?highlight=grafana%20plugin)).

The plugin list is specified in a variable called `grafana_plugins` that contains a dictionary of plugin specifications.

The values that can be used in each element are:

```yml
state - default(omit)
name - mandatory
plugin_url - default(omit)
plugins_dir - default(omit)
repo - default(omit)
version - default(omit)
```

An example that install a plugin and remove one (if is installed):

```yml
grafana_plugins:
  - { name: grafana-piechart-panel }
  - { name: grafana-clock-panel, state: absent }
```

### Container related variables

These variables defines the docker container that will run into the system.

| NAME | DEFAULT | DESCRIPTION   | file  |
|---|---|---|---|
| grafana_container_name  | grafana  | The name for the running container  | defaults/main.yml |
| grafana_container_hostname  | grafana | The hostname of the running container  | defaults/main.yml  |
| grafana_container_restart_policy  | unless-stopped  | docker restart policy  | defaults/main.yml  |
| grafana_publish_port  | 3000  | listen port for container service | defaults/main.yml  |
| grafana_container_image | grafana/grafana | container image | vars/main.yml |
| grafana_container_image_tag | latest | container image tag | vars/main.yml |

## Dependencies

  No Dependencies.

## Example Playbook

```yaml
- hosts: Server
  roles:
    - role: ppadial.docker-grafana
```

## License

MIT

## Meta

Paulino Padial – [@ppadial](https://github.com/ppadial) – github.com/ppadial

Distributed under the MIT license. See [LICENSE](LICENSE) for more information.

[https://github.com/ppadial/ansible-role-docker-grafana](https://github.com/ppadial/)

## Contributing

1. Fork it (<https://github.com/ppadial/ansible-role-docker-grafana/fork>)
2. Create your feature branch (`git checkout -b feature/fooBar`)
3. Commit your changes (`git commit -am 'Add some fooBar'`)
4. Push to the branch (`git push origin feature/fooBar`)
5. Create a new Pull Request

<!-- Markdown link & img dfn's -->
[wiki]: https://github.com/ppadial/ansible-role-docker-grafana/wiki

