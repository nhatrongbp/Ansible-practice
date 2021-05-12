## Contribution
**Controller node:192.168.1.105 (user:ep)**

**Managed node:** 

**192.168.1.106 (user:bp) run mariadb container**

**192.168.1.104 (user:sp) run wordpress container**
# On controller node:
### (Has alresady installed `Ansible` and `sshpass`)


## 1. Make your profect in folder named ansible-test
**mkdir at /home/ansible-test**

**Touch `ansible.cfg` `inventory.ini` `mariadb.yaml` `wordpress.yaml`**

## 2. Configure your settings in `ansible.cfg` file
```
[default]
host_key_checking = False
remote_user = ep
  ```
## 3. Define hosts, groups you want to manage in `inventory.ini` file
```
[db]
192.168.1.106
[wp]
192.168.1.104
[db:vars]
ansible_user=bp
ansible_ssh_user=bp
ansible_ssh_pass=123456
ansible_ssh_pass=123456
[wp:vars]
ansible_user=sp
ansible_ssh_user=sp
ansible_ssh_pass=123456
ansible_ssh_pass=123456
  ```
## 4. Write playbook to run mariadb container
```
---
- name: Install MariaDb
  hosts: db
  gather_facts: false

  tasks:
  - name: create volume
    become: yes
    command: docker volume create --name mariadb_data
  - name:  run image mariadb
    become: yes
    command: docker run -d --name mariadb --env ALLOW_EMPTY_PASSWORD=yes --env MARIADB_USER=bn_wordpress --env MARIADB_PASSWORD=bitnami --env MARIADB_DATABASE=bitnami_wordpress --network host --volume mariadb_data:/bitnami/mariadb bitnami/mariadb:latest

```
## 5. Write playbook to run wordpress container
```
---
- name: Install WordPress
  hosts: wp
  gather_facts: false

  tasks:
  - name: create volume for web
    become: yes
    command: docker volume create --name wordpress_data
  - name:  run image wordpress
    become: yes
    command:   docker run -d --name wordpress   --env WORDPRESS_DATABASE_HOST=192.168.5.10  --env ALLOW_EMPTY_PASSWORD=yes   --env WORDPRESS_DATABASE_USER=bn_wordpress   --env WORDPRESS_DATABASE_PASSWORD=bitnami   --env WORDPRESS_DATABASE_NAME=bitnami_wordpress   --network host   --volume wordpress_data:/bitnami/wordpress   bitnami/wordpress:latest

```
## 6. Play your playbook by command
```
& ansible-playbook -i inventory.ini mariadb.yaml -k -K
& ansible-playbook -i inventory.ini wordpress.yaml -k -K

```

**Result:**

<img src="https://scontent.fhan7-1.fna.fbcdn.net/v/t1.15752-9/183728289_504855450872346_4979660670135081098_n.png?_nc_cat=103&ccb=1-3&_nc_sid=ae9488&_nc_ohc=sPW3tvzmB9IAX-qgcpw&_nc_ht=scontent.fhan7-1.fna&oh=a56258ee2c2766d064dfccca207bd88c&oe=60BEEDC8">

  **Now access your application on managed node at [https://localhost:80](https://localhost:80)**
  
  .<img src="https://scontent.fhan7-1.fna.fbcdn.net/v/t1.15752-9/181454173_2836190489974120_3430845270581643751_n.png?_nc_cat=103&ccb=1-3&_nc_sid=ae9488&_nc_ohc=WnfmthD_j94AX9jFjxv&_nc_ht=scontent.fhan7-1.fna&oh=cd93527171e757839fa54713c197c95c&oe=60C08762">
  
  **More documents: [https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#directory-layout](https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#directory-layout)**
