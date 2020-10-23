Role Name
=========

Install Elasticsearch on a CentOS 7 or Rhel 7 OS.

Requirements
------------

None.

Role Variables
--------------

None.

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: "Install Logstash"
  hosts: logstash
  roles:
  - { role: plrr.logstash-install }
```

License
-------

Apache-2

Author Information
------------------

Check me on [LinkedIn](https://www.linkedin.com/in/phil-ranzato-47b8bb194)

Tasks Analysis
------------------

`01-install-java.yml`

```yaml
# yum install -y java-1.8.0-openjdk
- name: "Install Java OpenJDK (Logstash dependency)"
  yum:
    name: java-1.8.0-openjdk
    state: latest
  become: true
```

`02-install.yml`

```yaml
---
# rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
- name: "Import elasticsearch GPG key"
  rpm_key:
    state: present
    key: https://artifacts.elastic.co/GPG-KEY-elasticsearch
  become: true

# cat << EOF > /etc/yum.repos.d/logstash.repo
# [logstash-7.x]
# name=Elastic repository for 7.x packages
# baseurl=https://artifacts.elastic.co/packages/7.x/yum
# gpgcheck=1
# gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
# enabled=1
# autorefresh=1
# type=rpm-md
# EOF
- name: "Configure Logstash repository"
  copy:
    src: "logstash.repo"
    dest: "/etc/yum.repos.d/logstash.repo"
  become: true

# yum install -y logstash
- name: "Install Logstash"
  yum:
    name: logstash
    state: latest
  become: true

# systemctl enable logstash
# systemctl start logstash
# sudo /usr/share/logstash/bin/system-install /etc/logstash/startup.options systemd
- name: "Configure Logstash Service"
  shell: /usr/share/logstash/bin/system-install /etc/logstash/startup.options systemd
  notify: "Start Logstash"
  become: true
```
