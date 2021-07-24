# Docker Deployment from an Ansible Playbook
[![Build](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

---
## Description
It's just an ansible playbook and that the playbook installs docker and deploys a nodejs container through that playbook, all that plays are using with inbuilt ansible modules.

----
## Feature
- Easy to deploy a container with the help of an ansible-playbook
- All these plays are using inbuilt ansible modules 
- Docker installation and container spin up through the same playbook

----
## Pre-Requests
- Install Ansible on your Master Machine
- Basic Knowledge of ansible

##### Installation
[Ansible2](https://docs.ansible.com/ansible/2.3/index.html) (For your reference visit [How to install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html))

### Packages 
```sh
amazon-linux-extras install -y ansbile2
yum install -y git
```

##### Ansible Modules used
- [yum](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html) 
- [pip](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pip_module.html)
- [service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
- [docker_image](https://docs.ansible.com/ansible/2.8/modules/docker_image_module.html)
- [docker_container](https://docs.ansible.com/ansible/2.5/modules/docker_container_module.html)

----
## How to use
```sh
git clone https://github.com/yousafkhamza/docker-deployment-from-playbook.git
cd docker-deployment-from-playbook
```
```sh
ansible -i hosts -m ping all                 <--------- Establish/Check SSH connection via ansible
ansible-playbook -i hosts dockerinstallation.yml           <------------- playbook running script
```

----
## Output be like
```sh
PLAY [Installing docker through ansible] ***********************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************
ok: [172.31.10.48]

TASK [PIP Installation] ****************************************************************************************************************************
changed: [172.31.10.48]

TASK [Depandancy Task Install dokcer-py] ***********************************************************************************************************
changed: [172.31.10.48]

TASK [Docker Installtion] **************************************************************************************************************************
changed: [172.31.10.48]

TASK [Docker Service start and Enable] *************************************************************************************************************
changed: [172.31.10.48]

TASK [Docker image from docker hub] ****************************************************************************************************************
changed: [172.31.10.48]

TASK [Create a container from image] ***************************************************************************************************************
changed: [172.31.10.48]

PLAY RECAP *****************************************************************************************************************************************
172.31.10.48               : ok=7    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
## Point of view on Slave
```sh
[root@ip-172-31-10-48]# docker image ls
REPOSITORY          TAG       IMAGE ID       CREATED       SIZE
yousafkhamza/node   latest    39e4935f129b   6 weeks ago   50.8MB
[root@ip-172-31-10-48]# docker container ls
CONTAINER ID   IMAGE               COMMAND         CREATED         STATUS         PORTS                  NAMES
79867e5504e3   yousafkhamza/node   "node app.js"   8 minutes ago   Up 7 minutes   0.0.0.0:80->3000/tcp   ansible_nodejs
```

----
## Behind the code
_vim dockerinstallation.yml_
```sh
---
- name: 'Installing docker through ansible'
  hosts: all
  become: true
  vars:
    container_name: "ansible_nodejs"
    container_image: "yousafkhamza/node"
    container_port: "80:3000"
  tasks:
  
    - name: "PIP Installation"
      yum:
        name: pip
        state: present
        
    - name: "Depandancy Task Install dokcer-py"
      pip:
        name: docker-py
        state: present

    - name: "Docker Installtion"
      yum:
        name:
          - docker
        state: present

    - name: "Docker Service start and Enable"
      service:
        name: docker
        state: restarted
        enabled: true

    - name: "Docker image from docker hub"
      docker_image:
       name: "{{ container_image }}"
       source: pull

    - name: "Create a container from image"
      docker_container:
       name: "{{ container_name }}"
       image: "{{ container_image }}"
       ports: "{{ container_port }}"
       state: started
```
_vim hosts_
```sh
[slave]
172.31.10.48 ansible_user="ec2-user" ansible_port=22 ansible_ssh_private_key_file="/root/abcd.pem"
```
> Please note that you may use your slave server's details also please put your key absolute path and avoid unnecessary issues

### Output Screenshot
![alt_txt](https://i.ibb.co/x8DXspP/Screenshot-10.png)
----
## Reference

On this playbook was I used my old [nodjs](https://github.com/yousafkhamza/nodejs-dockerfile) container image so please be check the same. I have already explained how we create an image and container from the docker file. 

----
## Conclusion
It's a simple Ansible playbook how we pulled an image from docker hub and then how to spin up a container from that image through ansible. I just uploaded the same for educational purposes and this playbook we used inbuilt modules so it is should more helpful for freshers who started ansible and docker.

### ⚙️ Connect with Me 

<p align="center">
<a href="mailto:yousaf.k.hamza@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>
<a href="https://www.linkedin.com/in/yousafkhamza"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a> 
<a href="https://www.instagram.com/yousafkhamza"><img src="https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white"/></a>
<a href="https://wa.me/%2B917736720639?text=This%20message%20from%20GitHub."><img src="https://img.shields.io/badge/WhatsApp-25D366?style=for-the-badge&logo=whatsapp&logoColor=white"/></a><br />
