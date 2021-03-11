### Install Gitlab Omnibus Package ###
```
$ sudo dnf install -y curl policycoreutils openssh-server perl firewalld postfix
$ sudo systemctl enable sshd
$ sudo systemctl start sshd

$ sudo systemctl enable firewalld
$ sudo systemctl start firewalld

$ sudo systemctl enable postfix
$ sudo systemctl start postfix

$ curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash

$ sudo EXTERNAL_URL="https://gitlab.example.com" dnf install -y gitlab-ee
```