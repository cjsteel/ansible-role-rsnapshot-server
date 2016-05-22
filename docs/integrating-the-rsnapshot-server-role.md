
Role installation

## Configure inventory file

    cd project_root
    vi inventory/dev

### content example

Here we log into our rsnapshot server to be as the rsnapshot user and our local system will become our rsnapshot client.

    [rsnapshot-server]
    mag ansible_ssh_user=rsnapshot_user # frequently `root`
    
    [rsnapshot-client]
    sam ansible_connection=local

## Add role to your main playbook.

    cd project_root
    vi systems.yml

### Example entry

    ---
    - hosts: all
      tasks:
        - name: update apt cache
          become: true
          apt: update_cache=yes cache_valid_time=43200
          tags: [ 'packages' ]

    - include: rsnapshot-master.yml
    when: "'rsnapshot-master' in {{ group_names }}"

## Create the `rsnapshot-server.yml` playbook

    cd project_root
    vi rsnapshot-server.yml

### Content example

    - name: Backup Master
      hosts: rsnapshot-server
      sudo: yes
      roles:
        - rsnapshot-server
      vars:
        - rsnapshot_config_backup:
            - name: LOCALHOST
              points:
                - [backup, /home/, localhost/]
                - [backup, /etc/, localhost/]
                - [backup, /usr/local/, localhost/]
    #        - name: REMOTE_1
    #          points:
    #            - [backup, 'backupuser@192.168.1.10:/home', remote_1/]
    #            - [backup, 'backupuser@192.168.1.10:/etc', remote_1/]
    #            - [backup, 'backupuser@192.168.1.10:/var', remote_1/]
    #        - name: REMOTE_2
    #          points:
    #            - [backup, 'backupuser@192.168.1.20:/home', remote_2/]
    #            - [backup, 'backupuser@192.168.1.20:/etc', remote_2/]
    #            - [backup, 'backupuser@192.168.1.20:/var', remote_2/]


## review the roles default variables

    vi roles/rsnapshot-master/default/main.yml
