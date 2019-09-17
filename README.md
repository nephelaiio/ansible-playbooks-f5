# nephelaiio.playbooks-f5

[![Build Status](https://travis-ci.org/nephelaiio/ansible-playbooks-f5.svg?branch=master)](https://travis-ci.org/nephelaiio/ansible-playbooks-f5)

Ansible playbook to configure [F5](https://f5.com)assets for an application 

## Playbook descriptions

The following lists the group targets and descriptions for every playbook

| playbook | description                                                              | target   |
| ---      | ---                                                                      | ---      |
| app.yml  | configure pool/virtual server/irule/data group assets for an application | f5_nodes |
|          |                                                                          |          |

## Playbook variables

The following parameters are available/required for playbook invocation

### [pools.yml](pools.yml):
| required | variable        | description                      | default                                                         |
| ---      | ---             | ---                              | ---                                                             |
| *yes*    | f5_host         | f5 device to configure           | _undefined_                                                     |
| *yes*    | f5_user         | f5 user for f5_device            | _undefined_                                                     |
| *yes*    | f5_pass         | f5 pass for f5_device            | _undefined_                                                     |
| no       | f5_partition    | f5 node/pool partition           | /Common                                                         |
| no       | f5_manage_hosts | node management  toggle flag (*) | true                                                            |
| no       | f5_node_state   | f5 state for node                | poweredon *(poweredon/poweredoff/absent/present/shutdownguest)* |
| no       | f5_node_name    | f5 name for node                 | "{{ inventory_hostname }}"                                      |
| no       | f5_node_address | f5 address for node              | "{{ ansible_host }}"                                            |
| no       | f5_node_pools   | f5 pools node belongs to         | []                                                              |

### [virtual_servers.yml](pools.yml):
| required | variable                 | description                        | default     |
| ---      | ---                      | ---                                | ---         |
| *yes*    | f5_host                  | f5 device to configure             | _undefined_ |
| *yes*    | f5_user                  | f5 user for f5_device              | _undefined_ |
| *yes*    | f5_pass                  | f5 pass for f5_device              | _undefined_ |
| no       | f5_partition             | f5 virtual server partition        | /Common     |
| no       | f5_manage_vss            | f5 vip management  toggle flag (*) | true        |
| no       | f5_vss                   | f5 virtual servers to configure    | []          |

## Data Formats

### Pools
```{yaml}
f5_node_pools:
  - state: present
    name: metabase.web.http
    state: present
    port: 80
    monitors:
      - /Common/http
```

### Virtual Servers

```{yaml}
f5_manage_vss: "{{ inventory_hostname == (groups['metabase_app'] | first) }}"
f5_vss:
  - state: present
    name: metabase.http
    address: 10.50.78.6
    port: 80
    pool: "{{ f5_pools.metabase.name }}"
    profiles:
      - http
    irules:
      - /Common/_sys_https_redirect
  metabase_https:
    state: present
    name: metabase.https
    address: 10.50.78.6
    port: 443
    pool: "{{ f5_pools.metabase.name }}"
    profiles:
      - /Common/http
      - /Common/client.example.com
```

## Example Invocation

```
git checkout https://galaxy.ansible.com/nephelaiio/ansible-playbooks-f5 f5
ansible-playbook -i inventory/ f5/app.yml
```

## License

This project is licensed under the terms of the [MIT License](/LICENSE)
