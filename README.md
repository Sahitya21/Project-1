# Project-1
**CI/CD PIPELINE PROJECT DEPLOYMENT USING TOMCAT**

** Project Overview:
In this project, I designed and implemented a Continuous Integration and Continuous Deployment (CI/CD) pipeline for a Spring Boot application using various tools, including Jenkins, Git, Maven, SonarQube, Nexus, and Tomcat. The primary goal was to automate the software development lifecycle, enhance code quality, and streamline the deployment process.

** Tools and Technologies used
-Git: For version control and managing source code.
-Maven : To Automate the build process and manage project dependencies.
-Jenkins: A CI/CD server that automates the building, testing and deployment process.
-SonarQube: For static code analysis to ensure code quality and maintainabiliity.
-Nexus: An artifact repository used for storing build artifacts and dependencies.
-Docker: For containerizing the application
-Tomcat: A webserver for deploying the application.

** PROJECT WORKFLOW WITH INSTALLATION STEPS:
AWS Console Setup: 
1. Launch EC2 Instances in AWS
creating 4 Instances: Jenkins, SonarQube, Nexus, and Tomcat, each on a separate Amazon Linux 2 EC2 instance
Instance Type: Choose t2.medium(provides 2 vCPUs and 4 GB RAM) or t2.large 
keypair: create a new keypair or use an existing one.
Security Groups: Configured for SSH access and All Traffic for each instance.
SSH Connections: Made using Git Bash and the provided .pem key pair.
Launch the Instance: Once all settings are configured, click Launch Instance.

View Instances:
After launching, navigate to the EC2 Dashboard > Instances to view your running instances. Wait until their status shows running.

NOTE:

Jenkins: Port 8080: For web access to Jenkins.

SonarQube: Port 9000: For web access to SonarQube.

Nexus: Port 8081: For web access to Nexus.

Tomcat: Port 8080: For accessing Tomcat.


2. Connecting to EC2 Instances Using Git Bash
Once your instances are running, you can connect to them using Git Bash with the SSH key pair that you downloaded.

Open Git Bash:On your local machine
Navigate to the Directory with the Key Pair:
Use the following SSH command to connect to each instance 
connect to your EC2 instance using Git Bash:
now ready to install and configure the necessary CI/CD tools on each instance.



3.Tool Installation on Each Instance
1.Jenkins Instance: Installing Java, Git, Maven, Jenkins, and Docker
--Update the package list:
>>sudo yum update -y

--Install Java (OpenJDK 11):
>>sudo amazon-linux-extras install java-openjdk11 -y

--Verify the Java installation:
>>java -version

-> Install Git
>>sudo yum install git -y
--Verify Git installation:
>>git --version
check the version of Git installed.

->Install Maven
Maven is required to build Java projects.
>>sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
  sudo sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
  sudo yum install -y apache-maven

--Verify Maven installation:
>>mvn -version

->Install Jenkins
sudo wget -O /etc/yum.repos.d/jenkins.repo \https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
Install Jenkins:
sudo yum install jenkins -y
Start Jenkins:
sudo systemctl start jenkins
Enable Jenkins to start on boot:
sudo systemctl enable jenkins
Check Jenkins status:
sudo systemctl status jenkins

Access Jenkins:
Jenkins runs on port 8080 by default.
To access Jenkins, open your browser and copy jenkins-public-ip:8080.

Unlock Jenkins:
You’ll need the initial admin password to unlock Jenkins.
Retrieve the password using this command:
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Copy this password, paste it into the Jenkins web UI, and follow the setup instructions.

->Install Docker
Docker is used to create and manage containers in the CI/CD pipeline.

Install Docker:
>>sudo yum install docker -y
Start Docker service:
>>sudo systemctl start docker
Enable Docker to start on boot:
>>sudo systemctl enable docker

Verify Docker installation:
>>docker --version


2. SonarQube Instance: Installing Java, PostgreSQL, and SonarQube
Connect to SonarQube Instance via SSH
Use Git Bash to connect to your SonarQube EC2 instance:
-> Install Java (OpenJDK 11)
sudo amazon-linux-extras install java-openjdk11 -y
Verify Java installation:
java -version

-> Install SonarQube:
sudo wget -O /etc/yum.repos.d/sonar.repo http://downloads.sourceforge.net/project/sonar-pkg/rpm/sonar.repo
sudo yum install sonar -y
service sonar start

SonarQube runs on port 9000. Open your browser and copy sonarqube-public-ip:9000.

3. Nexus Instance: 
Connect to Nexus Instance via SSH
Use Git Bash to connect to your Nexus EC2 instance:
Install Java (OpenJDK 11)
Nexus requires Java to run.
->Install Java:
sudo amazon-linux-extras install java-openjdk11 -y
Verify Java installation:
java -version
3. Install Nexus Repository Manager
cd /opt/
ls
sudo wget https://download.sonatype.com/nexus/3/latest-unix.tar.gzsudo wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz

tar -xvf latest-unix.tar.gz
ls
mv nexus-3.34.0-01 nexus3
chown -R ec2-user:ec2-user nexus3 sonatype-work
cd nexus3/
cd bin
vi nexus.rc
ln -s /opt/nexus3/bin/nexus /etc/init.d/nexus
cd /etc/init.d/
chkconfig --add nexus
chkconfig nexus on
sudo service nexus start
cat /opt/sonatype-work/nexus3/admin.password
cd /opt/
cd nexus3/
cd bin
ls
cd ..
cd etc/
vi nexus-default.properties
sudo service nexus stop
sudo service nexus start

Nexus runs on port 8081. Open your browser and copy nexus-public-ip:8081.

4.Tomcat Instance: Installing Java and Tomcat
Connect to Tomcat Instance via SSH
Use Git Bash to connect to your Tomcat EC2 instance:

--Install Java (OpenJDK 11)
sudo amazon-linux-extras install java-openjdk11 -y
--Verify Java installation:
java -version
--Install Apache Tomcat
wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.73/bin/apache-tomcat-9.0.73.tar.gz
--Extract Tomcat:
tar -xvf apache-tomcat-9.0.73.tar.gz
--Move Tomcat to /opt:
sudo mv apache-tomcat-9.0.73 /opt/tomcat
Start Tomcat:
/opt/tomcat/bin/startup.sh
Access Tomcat:
Tomcat runs on port 8080. Open your browser and copy tomcat-public-ip:8080.

** CI/CD Pipeline Implementation

After the tools were installed, I created a Jenkins pipeline to automate the entire workflow. Here’s how the pipeline operates:

Source Code Management: Developers commit their code to a Git repository. This triggers the Jenkins pipeline automatically.

Build Stage: Jenkins pulls the latest code and uses Maven to build the application. It compiles the code, resolves dependencies, and packages the application into a deployable artifact (a WAR file in this case).

Static Code Analysis: After the build, Jenkins invokes SonarQube to analyze the code for potential issues. SonarQube checks for bugs, code smells, and security vulnerabilities, providing developers with immediate feedback.

Artifact Management: Once the build is successful and the code quality is validated, Jenkins publishes the artifact to Nexus. This ensures that we have a centralized repository for versioning and managing our build artifacts.

Deployment Stage: Finally, Jenkins deploys the artifact from Nexus to the Tomcat server. I configured Tomcat's manager to allow Jenkins to perform deployments automatically, reducing the manual effort and potential for errors.

**Benifits of CI/CD pipeline project:
Speed: Faster development cycles.
Quality: Higher code quality.
Consistency: Reliable deployments.
Efficiency: Streamlined processes.
Feedback: Immediate code analysis.
Automation: Reduced manual effort.
