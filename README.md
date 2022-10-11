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

## kubernetes install
