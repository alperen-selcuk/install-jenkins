# install-jenkins

## ubuntu install

jenkins pre-req olarak java istemektedir.

```
sudo apt-get update -y && sudo apt-get install openjdk-11-jre -y
```

daha sonra jenkins yükleyebiliriz.

```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
  
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
  
sudo apt-get update -y && sudo apt-get install jenkins -y
```

default port 8080 olarak çalışmaktadır.

> **Warning**  defaultda cloudda inactive ama sizde aktive olması ihtimaline karşın fw ye portu eklemeniz iyi olur.

```
sudo ufw allow 8080
```


> **Warning**  eğer java ile ilgili bir hata alıyorsanız JAVA_HOME u belirtmeniz gerek. aşağıda benim javanın dizini var bu şekilde ekleuebilirsiniz.

```
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64

export PATH=$PATH:$JAVA_HOME/bin
```

> **Warning**  maven kullanmak isterseniz ve hata alırsanız, jenkinse "global tool configuration" üzerinden mvn pathini belirtmeniz gerekiyor

## docker install

önce bir docker volume yaratacağız yaptığımız configler container silinirse gitmemesi için.

```
docker volume create jenkins_path

docker run --name jenkins -d -v jenkins_path:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

eğer jenkins ile docker build ve push yapmak gerekiyorsa slave olarak dind kullanmanız ve volume olarak docker.sock u da mount etmeniz gerekiyor. jenkins imagei üzerine docker yükleyerek bunun üstesinden gelebilirsiniz.

öncelikle bir Dockerfile gerekiyor

```
FROM jenkins/jenkins:jdk11
 
USER root

RUN apt-get update -y

RUN apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

RUN mkdir -p /etc/apt/keyrings

RUN  curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

RUN echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

RUN apt-get update

RUN apt-get install -y docker-ce docker-ce-cli containerd.io 

RUN usermod -aG docker jenkins
```

docker build -t jenkins:dind .

yapıp build aldıktan sonra, docker.sock u mount ederek çalıştıracağız

```
docker run --name jenkins -d -p 8080:8080 -p 50000:50000 -v /tmp/jenkins:/var/lib/jenkins -v /var/run/docker.sock:/var/run/docker.sock jenkins:dind
```

## kubernetes install
