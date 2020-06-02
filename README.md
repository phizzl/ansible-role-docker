# Ansible role docker-compose
A role for booting a docker-compose setup

## Requirements
- Hosts should be bootstrapped for ansible usage (have python,...)
- `docker` and `docker-compose` should be installed. This can be archived by using the role [`nickjj.docker`](https://github.com/nickjj/ansible-docker) for example.

## Role Variables

| Variable | Description | Default value |
|----------|-------------|---------------|
| `docker_compose_src`| See details below | / |

### `docker_compose_src` details

The docker-compose list is used to define the setups to run or terminate per host.   Each item in
the list can have following attributes:

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `dest` | Destination to store the setup | yes | / |
| `git` | See [official docs](https://docs.ansible.com/ansible/latest/modules/git_module.html) | no | / |
| `definition` | Define docker-compose file in this YAML. You can put more YAML or a string containing YAML here | no | / |
| `files` | The docker-compose.yml files that should be used when running the setup  | no | ["docker-compose.yml"] |
| `state` | state of the setup (present, stopped or absent) | no | present |
| `build` | docker-compose build?  | no | false |
| `pre_commands` | List of shell commands to run before docker-compose is started or stopped | no | / |
| `post_commands` | List of shell commands to run after docker-compose is started or stopped | no | / |
| `templates` | Templates to be copied to the setup (before running pre commands, path relative to `dest`) | no | [] |

## Example Playbooks

### Run from git
```yaml
---
- hosts: docker
  roles:
    - role: phizzl.dockercompose
      vars:
        docker_compose_src:
          - dest: /tmp/test-sentry
            git:
              repo: https://github.com/getsentry/onpremise.git
              version: 10.0.0
            pre_commands:
              - "[ -f installed.lock ] || (CI=1 bash install.sh && touch installed.lock)"
```

### Run by definition
```yaml
---
- hosts: docker
  roles:
    - role: phizzl.dockercompose
      vars:
        docker_compose_src:
          - dest: /tmp/test-apache
            definition:
              version: '3.3'
              services:
                web:
                  image: phizzl/php:7.4-apache-ubuntu-xenial
```
