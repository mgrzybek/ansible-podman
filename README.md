ansible-podman
==============

This role installs podman and configure their storage.

Requirements
------------

If ZFS is used, an existing zpool has to be provided.

Role Variables
--------------


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
