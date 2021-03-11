### Install Gitlab Omnibus Package ###
```
$ sudo dnf install -y curl policycoreutils openssh-server perl firewalld postfix
$ sudo systemctl enable sshd
$ sudo systemctl start sshd

$ sudo systemctl enable firewalld
$ sudo systemctl start firewalld

$ sudo systemctl enable postfix
$ sudo systemctl start postfix

$ sudo firewall-cmd --permanent --add-service=http
$ sudo firewall-cmd --permanent --add-service=https
$ sudo systemctl reload firewalld

$ curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash

$ sudo EXTERNAL_URL="https://gitlab.example.com" dnf install -y gitlab-ee
```

### Install PostgreSQL ###
```
# Install the repository RPM:
sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm

# Disable the built-in PostgreSQL module:
sudo dnf -qy module disable postgresql

# Install PostgreSQL:
sudo dnf install -y postgresql13-server postgresql13-contrib

# Optionally initialize the database and enable automatic start:
sudo /usr/pgsql-13/bin/postgresql-13-setup initdb
sudo systemctl enable postgresql-13
sudo systemctl start postgresql-13
