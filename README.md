ansible-podman
==============

This role installs podman and configure their storage.

Requirements
------------

If ZFS is used, an existing zpool has to be provided.

Role Variables
--------------

```yaml
# Configure podman for non-root users and groups
podman_users: []
podman_groups: []

podman_container_restartsec: 10

# Storage management (storage.conf)
podman_containers_storage: /etc/containers/storage.conf
podman_containers_storage_driver: overlay
podman_containers_storage_device:
podman_containers_storage_root: /var/run/containers
podman_containers_storage_runroot: '{{ podman_containers_storage_root }}/storage'
podman_containers_storage_graphroot: '{{ podman_containers_storage_root }}/storage'
podman_containers_storage_additionalimagestores: []
podman_containers_storage_size:

# Package management
podman_package_state: present # present or latest
cache_timeout: 600

# Array of container definitions
podman_containers: []
#  - name:
#    run_opts:
#    image:
#    command:
#    command_opts:
#    selinux_sandboxing_paths: []

# Array of pods definitions
podman_pods: []
#  - name:
#    description:
#    opts:

# Proxy management
podman_http_proxy:
podman_https_proxy: '{{ podman_http_proxy }}'
podman_http_no_proxy:

# Do we pull concainers before starting the service?
podman_pull_containers: false
```

Dependencies
------------

None.

Example Playbook
----------------

Install podman and configure storage for root user (ZFS on default mountpoints):

    - hosts: servers
      roles:
         - role: ansible-podman
      vars:
         podman_containers_storage_driver: zfs
         
Install podman and create a systemd-driven container:

    - hosts: servers
      roles:
         - role: ansible-podman
      vars:
         podman_post_config_restart_on_change: true
         podman_pull_containers: true
         podman_pods:
           - name: mypod
             description: My custom POD
             opts: "--publish 80:8080"
         podman_containers:
           - name: traefik
             description: Traefik reverse proxy
             after: my-pod.pod.service
             selinux_sandboxing_paths:
             - /etc/traefik
             run_opts:
             - --pod appliance
             - --rm
             image: docker.io/library/traefik
             command: traefik
             command_opts:
             - --entrypoints.web.address=:8080
             - --accesslog=false
             - --accesslog.format=json
             - --log=true
             - --log.format=json

Install podman and configure storage for non-root users:

    - hosts: servers
      roles:
         - role: ansible-podman
      vars:
         podman_users:
           - name: john
             graphroot: /home/john/.local/share/containers/storage
             runroot: /home/john/.local/share/containers/storage
             storage_driver: overlay


License
-------

GPL-3+

Author Information
------------------

Mathieu GRZYBEK
