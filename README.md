# Ansible role docker
A role for booting a docker-compose setup

## Requirements
- Hosts should be bootstrapped for ansible usage (have python,...)
- Root privileges, eg `become: yes`

## Role Variables

| Variable | Description | Default value |
|----------|-------------|---------------|
| `docker_user` | The user that runs the setup | `{{ ansible_user }}` |
| `docker_group`| The group that is allowed to use docker. The user `docker_user` is added to this group when defined | / |


#### `docker_compose_src` details

The docker-compose list is used to define the setups to run or terminate per host.   Each item in
the list can have following attributes:

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `dest` | Destination to store the setup | yes | / |
| `repo` | A GIT repository to clone the docker-compose setup from | no | / |
| `accept_hostkey` | Accept hostkeys when using a GIT repo | no | yes |
| `version` | The version for the GIT repo to checkout | no | HEAD |
| `key_file` | The private key file to use for GIT | no | / |
| `inline` | Inline docker-compose.yml | no | / |
| `files` | The docker-compose.yml files that should be used when running the setup  | no | [] |
| `state` | state of the setup (present, stopped, restarted or absent) | no | present |
| `build` | docker-compose build?  | no | false |
| `pre_cmd` | Inline script to run before docker-compose is started or stopped | no | / |
| `post_cmd` | Inline script to run after docker-compose is started or stopped | no | / |

###### Example `vd_docker_compose_src`

```yaml
docker_compose_src:
  - dest: /opt/vd-redmine
    repo: https://github.com/bitnami/bitnami-docker-redmine.git
    files:
      - docker-compose.yml
      - docker-compose.development.yml

  - dest: /opt/vd-jenkins
    inline: |
      version: 3
      services:
        db:
          image: mysql:5.7
          restart: always
          environment:
            MYSQL_DATABASE: 'db'
            MYSQL_USER: 'user'
            MYSQL_PASSWORD: 'password'
            MYSQL_ROOT_PASSWORD: 'password'
          ports:
            - '3306:3306'
          volumes:
            - my-db:/var/lib/mysql
    
  - dest: /opt/vd-project
    repo: https://github.com/bitnami/bitnami-docker-jenkins.git
    state: absent
```

## Dependencies

* nickjj.docker

## Example Playbook  

```yaml
---
- hosts: docker
  roles:
    - role: phizzl.docker
      become: yes
```
