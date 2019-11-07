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
| `repo` | A GIT repository to clone the docker-compose setup from | no | / |
| `accept_hostkey` | Accept hostkeys when using a GIT repo | no | yes |
| `version` | The version for the GIT repo to checkout | no | HEAD |
| `key_file` | The private key file to use for GIT | no | / |
| `inline` | Inline docker-compose.yml | no | / |
| `files` | The docker-compose.yml files that should be used when running the setup  | no | [] |
| `state` | state of the setup (present, stopped, restarted or absent) | no | present |
| `build` | docker-compose build?  | no | false |
| `pre_commands` | List of shell commands to run before docker-compose is started or stopped | no | / |
| `post_commands` | List of shell commands to run after docker-compose is started or stopped | no | / |

### `docker_compose_src` example

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

## Example Playbook

```yaml
- hosts: docker
  roles:
    - role: phizzl.dockercompose
```

### Running docker as non-ansible user

If you want to start the Docker setup with a different user than the `ansible_user` you need to become that user.  
For that you need the permission for `become`. See example below.

```yaml
- hosts: docker
  become: yes
  roles:
    - role: phizzl.dockercompose
      become_user: docker
```
