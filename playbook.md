## Adhoc commands
- Check system info
    ```sudo ansible web -a "uname -a"```

- Check the system date
    ```sudo ansible web -a "date"```

- Check RAM
    ```sudo ansible web -a "free"```

- list contents of pwd
    ```sudo ansible web -a "ls -a"```

## Install Nginx

```yaml
# YAML file start --- three dashes

# Why yaml
# why playbooks
# create a playbook to install ingnx in web node

---

# which host to perform the task must match the name of the host in the hosts file
- hosts: web

# see the log by gathering facts
  gather_facts: yes

# root or admin access
  become: true

# add the instructions - install nginx: in web node
  tasks:
  - name: Installing Nginx
    apt: pkg=nginx state=present

# check status of nginx is actively running
# adhoc command to check the status

# configure reverse proxy

  - name: Customize Nginx default configuration file
    lineinfile:
      path: /etc/nginx/sites-available/default
      regexp: '^(\s*try_files.*)$'
      line: '        proxy_pass http://localhost:3000;'

# restart nginx

  - name: Restart Nginx service
    service:
      name: nginx
      state: restarted
```

## Install nodejs and start the app
```yaml
---
# which host to perform
- hosts: web

# see the log by gathering facts
# gathering_facts: yes

# admin access
  become: true

# install nodejs
  tasks:
  # add the instructions  -  install node 12 with pm2 and run the app
  - name: Installing node v 12the gog key for nodejs
    apt_key:
      url: "https://deb.nodesource.com/gpgkey/nodesource.gpg.key"
      state: present

  - name: Add NodeSource repository
    apt_repository:
      repo: "deb https://deb.nodesource.com/node_12.x {{ ansible_distribution_release }} main"
      state: present


  - name: Clone the Git repository
    git:
      repo: https://github.com/jungjinggg/tech241_sparta_app.git
      dest: /home/ubuntu/app

    become: yes

  - name: Install Node.js
    apt:
      name: nodejs
      state: present
      update_cache: yes

#  - name: Install PM2
#    npm:
#      name: pm2
#      global: yes
#      state: present

  - name: Install app dependencies
    command: npm install
    args:
      chdir: /home/ubuntu/app/app/

#  - name: Stop PM2 processes
#    shell: pm2 kill

  - name: Start the Node.js app
    shell: npm start
    args:
      chdir: /home/ubuntu/app/app
```

