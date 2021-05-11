## Contribution
**Controller node:192.168.1.105 (user:ep)**

**Managed node: 192.168.1.106 (user:bp)**
# On controller node:
## 1. Install `Ansible` and `sshpass`
**Install Ansible via apt**
```
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible
```
**Check Ansible version**
```
$ ansible --version
```
**Install `sshpass` for SSH password-based login**
```
$ sudo apt-get install -y sshpass

```

## 2. Make your first profect in folder named ansible-test
**mkdir at /home/ansible-test**

**Touch `ansible.cfg` `inventory.ini` and `your-playbook.yaml`**

## 3. Configure your settings in `ansible.cfg` file
```
[default]
host_key_checking = False
remote_user = ep
  ```
## 4. Define hosts, groups you want to manage in `inventory.ini` file
```
[vm]
192.168.1.106
[vm:vars]
ansible_user=bp
ansible_ssh_user=bp
ansible_ssh_pass=123456
ansible_ssh_pass=123456
  ```
## 4. Write your first playbook to deploy wordpress on managed node
```
---
- name: Install MariaDb
  hosts: vm
  gather_facts: false

  tasks:
  - name: create network
    become: yes
    command: docker network create wordpress-network
  - name: create volume for db
    become: yes
    command: docker volume create --name mariadb_data
  - name:  run image mariadb
    become: yes
    command: docker run -d --name mariadb --env ALLOW_EMPTY_PASSWORD=yes --env MARIADB_USER=bn_wordpress --env MARIADB_PASSWORD=bitnami --env MARIADB_DATABASE=bitnami_wordpress --network wordpress-network --volume mariadb_data:/bitnami/mariadb bitnami/mariadb:latest
  - name: create volume for web
    become: yes
    command: docker volume create --name wordpress_data
  - name:  run image wordpress
    become: yes
    command: docker run -d --name wordpress -p 80:8080 -p 443:8443  --env ALLOW_EMPTY_PASSWORD=yes --env WORDPRESS_DATABASE_USER=bn_wordpress  --env WORDPRESS_DATABASE_PASSWORD=bitnami  --env WORDPRESS_DATABASE_NAME=bitnami_wordpress --network wordpress-network --volume wordpress_data:/bitnami/wordpress  bitnami/wordpress:latest
```
## 5. Play your playbook by command
```
& ansible-playbook -i inventory.ini deploy-wp.yaml -k -K
```

**Result:**

<img src="https://scontent.fhan7-1.fna.fbcdn.net/v/t1.15752-9/183728289_504855450872346_4979660670135081098_n.png?_nc_cat=103&ccb=1-3&_nc_sid=ae9488&_nc_ohc=sPW3tvzmB9IAX-qgcpw&_nc_ht=scontent.fhan7-1.fna&oh=a56258ee2c2766d064dfccca207bd88c&oe=60BEEDC8">

  **Now access your application on managed node at [https://localhost:80](https://localhost:80) or [https://localhost:443](https://localhost:443)**
  
  .<img src="https://scontent.fhan7-1.fna.fbcdn.net/v/t1.15752-9/181454173_2836190489974120_3430845270581643751_n.png?_nc_cat=103&ccb=1-3&_nc_sid=ae9488&_nc_ohc=WnfmthD_j94AX9jFjxv&_nc_ht=scontent.fhan7-1.fna&oh=cd93527171e757839fa54713c197c95c&oe=60C08762">
  
  **More documents: [https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#directory-layout](https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#directory-layout)**
