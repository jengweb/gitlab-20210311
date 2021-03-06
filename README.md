### Install Gitlab Omnibus Package ###
```
sudo dnf install -y curl policycoreutils openssh-server perl firewalld postfix
sudo systemctl enable sshd
sudo systemctl start sshd

sudo systemctl enable firewalld
sudo systemctl start firewalld

sudo systemctl enable postfix
sudo systemctl start postfix

sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld

curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash

sudo EXTERNAL_URL="https://gitlab.example.com" dnf install -y gitlab-ee
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
```

### Install JFrog ###
```
wget -O artifactory-pro.rpm "https://releases.jfrog.io/artifactory/artifactory-pro-rpms/jfrog-artifactory-pro/jfrog-artifactory-pro-[RELEASE].rpm"

sudo yum install ./artifactory-pro.rpm -y

sudo systemctl start artifactory.service
```

### Install SonarQube ###
```
sudo yum install -y wget unzip java-11-openjdk-devel firewalld

sudo sysctl -w vm.max_map_count=262144

sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.7.0.41497.zip

unzip sonarqube-8.7.0.41497.zip
sudo mv sonarqube-8.7 /opt/sonarqube

sudo useradd sonar
sudo chown -R sonar:sonar /opt/sonarqube
```

#### Then create the file /etc/systemd/system/sonarqube.service based on the following ####
```
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=simple
User=sonar
Group=sonar
PermissionsStartOnly=true
ExecStart=/bin/nohup /usr/bin/java -Xms32m -Xmx32m -Djava.net.preferIPv4Stack=true -jar /opt/sonarqube/lib/sonar-application-8.7.0.41497.jar
StandardOutput=syslog
LimitNOFILE=131072
LimitNPROC=8192
TimeoutStartSec=5
Restart=always
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

```
sudo systemctl enable sonarqube.service
sudo systemctl start sonarqube.service
```

```
sudo su - sonar
cd /opt/sonarqube
./bin/linux-x86-64/sonar.sh start

```

### Scan Code by Maven ###
Create Project from SonarQube
```
./mvnw clean test   

./mvnw clean package

mvn sonar:sonar \
  -Dsonar.projectKey=[Project Name] \
  -Dsonar.host.url=http://[SonarQube Server]:9000 \
  -Dsonar.login=[Project Token]

```