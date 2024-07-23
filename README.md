 Jenkins CI/CD Pipeline
Implemented a comprehensive CI/CD pipeline for Java-based applications using Jenkins declarative pipelines. 🎉

this part has no deal with ci/cd s just for run the app locally to check it work properly or not
for this clone the given repo  cd project path 
now we have to insttall app on local
step:1 Execute the Maven targets to generate the artifacts

mvn clean package
The above maven target stroes the artifacts to the target directory. You can either execute the artifact on your local machine (or) run it as a Docker container.

Note: To avoid issues with local setup, Java versions and other dependencies, I would recommend the docker way. **

step: 2
The Docker way
Build the Docker Image

docker build -t ultimate-cicd-pipeline:v1 .
docker run -d -p 8010:8080 -t ultimate-cicd-pipeline:v1
step:3
Hurray !! Access the application on http://<ip-address>:8010



so now using ci/cd we deploy app on kubenetes 

continues integratiion:
step:1 source code that we allready see and run app locally
step : 2 create ec2 instance t2.large
step3: ssh to ec2 instance and now we have to install java and jenkins
    1. so first instll java because java is prerequisites for jenkins and jenkins is java application so lets install it
      sudo apt update
sudo apt install openjdk-11-jre
vefify jav is installed or not
java -version
step :2 install jenkins  command:curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins 
note:jenkins is run on defaut port 8080
Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

EC2 > Instances > Click on
In the bottom tabs -> Click on Security
Security groups
Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed All traffic).

chhalanged faced:
so during instalation of jenkins my intalled verison is jdk-11 but it require 17 so for make sure yourjava version is latest.then restrt the jenkins it take my 30 min to solve this i see log deatil debugging mode to find the error.

now go to jenkins you see password file just sudo cat the file for jenkins password. install suggested plugins make sure you save the generate key for future use
so in jenkins first thing i have to do is write pipeline  so we can do this in 2 way 1.through grovy scripting and through  script for scm mean the source code of pipeline stored in git
add path of repo select brnch and add jenkinsfile repo here interview question:
1.is jenkins file in any location?
yes jenkins file can be stored in any location
2.is jankins file have any name?  yes namimg conversion is anything
now apply it
good way to write jenkins pipeline is used docker as a agent reason you have to do lesser configuration
two thind you have to take care of 
1.install docker pipline plugins in jenkins
2.choose image widey
2. i installed docker continer which has allready maven installed  so next installation is sonar server

now install sonar server plugin in jenkins(sonarqube scnnner)
next step is to install sonar server on ec2 instance you can install it on anywhere but your jenkinds is connected to aws and if you dont want anyy vpc peering or network issue installed on aws ec2

step:1
install unzip first
first add sonar user
sudo adduser sonarqube
sudo -su sonarqube
interiw question:
what is different between adduser and useradd
- **useradd**: A low-level command that creates a user with default settings; additional configuration is often needed.
- **adduser**: A higher-level, interactive command that creates a user with default settings, including home directory and shell.

step 2:
sudo chown -R sonarqube:sonarqube /home/sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip sonarqube-9.4.0.54424.zip
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd /home/sonarqube/sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start

Hurray !! Now you can access the SonarQube Server on http://<public ip of ec2 insatnce>:9000
by default sonar is run on 9000
user: admin
pass : admin
then update password  and you see sonar dashboard
 now see to diagram




