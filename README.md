# maven-java-jenkin


node {
    def jdkHome = tool 'JDK21'
    def mavenHome = tool 'Maven3'

    stage('Checkout') {
        git branch: 'main',
            url: 'https://github.com/haleema91/javacseh'
    }

    stage('Build') {
        withEnv(["JAVA_HOME=${jdkHome}",
                 "PATH+JAVA=${jdkHome}\\bin",
                 "PATH+MAVEN=${mavenHome}\\bin"]) {
            bat 'mvn clean compile'
        }
    }

    stage('Test') {
        withEnv(["JAVA_HOME=${jdkHome}",
                 "PATH+JAVA=${jdkHome}\\bin",
                 "PATH+MAVEN=${mavenHome}\\bin"]) {
            bat 'mvn test'
        }
    }

    stage('Package') {
        withEnv(["JAVA_HOME=${jdkHome}",
                 "PATH+JAVA=${jdkHome}\\bin",
                 "PATH+MAVEN=${mavenHome}\\bin"]) {
            bat 'mvn package'
        }
    }

    stage('Archive') {
        archiveArtifacts artifacts: 'target/*.jar'
    }
}


#maven-web-jenkins

node {
    def jdkHome = tool 'JDK21'
    def mavenHome = tool 'Maven3'

    withEnv(["JAVA_HOME=${jdkHome}",
            "PATH+JAVA=${jdkHome}\\bin",
            "PATH+MAVEN=${mavenHome}\\bin"]) {

        stage('Checkout') {
            git branch: 'main',
                url: 'https://github.com/haleema91/cs-e34.git'
        }

        stage('Build') {
            bat 'mvn clean compile'
        }

        stage('Test') {
            bat 'mvn test'
        }

        stage('Package WAR') {
            bat 'mvn package'
        }

        stage('Archive WAR') {
            archiveArtifacts artifacts: 'target/*.war'
        }

        stage('Deploy to Tomcat') {
            bat 'copy /Y target\\*.war C:\\apache-tomcat-10.1.62\\webapps\\'
        }
    }
}


# amazon ec2
http://18.207.128.214/





Here is the complete, end-to-end sequence to deploy your Java Maven project (`javacseh`) on an AWS Academy EC2 instance and serve its output live over HTTP.

### 1. Start the AWS Academy Learner Lab

1. Log in to `awsacademy.instructure.com` and open your course.
2. Under **Modules**, confirm the **Module Knowledge Check** under *AWS Academy Learner Lab Compliance and Security* has a green checkmark (score at least 70.0), then click **Launch AWS Academy Learner Lab**.
3. If the frame appears blank, allow third-party cookies for `awsacademy.instructure.com` and disable ad-blockers, then reload.
4. Click **Start Lab**, wait for the **AWS** dot to turn **green**, and click **AWS** to open the Management Console.
* *Verification:* The AWS Management Console opens in a new browser tab.



### 2. Launch an EC2 Instance

1. In the AWS Console search bar, open **EC2** and click **Launch instance**.
2. Configure the instance:
* **Name:** `Project-Deployment-Server`
* **AMI:** **Amazon Linux 2023**
* **Instance type:** `t2.micro` or `t3.micro`
* **Key pair (login):** Select your `.pem` key pair (e.g., `ec2-deploy-key.pem` saved in `C:\Users\hp\Downloads\`).
* **Network settings:** Enable **Allow SSH traffic** (`0.0.0.0/0`), **Allow HTTP traffic from the internet**, and **Allow HTTPS traffic from the internet**.


3. Click **Launch instance**, go to **Instances**, and copy your instance's **Public IPv4 address**.
* *Verification:* **Instance state** shows **Running** and **Status check** shows `2/2 checks passed`.



### 3. Connect via SSH from PowerShell

1. Open Windows PowerShell and restrict permissions on your `.pem` key file:
```powershell
icacls "C:\Users\hp\Downloads\ec2-deploy-key.pem" /inheritance:r
icacls "C:\Users\hp\Downloads\ec2-deploy-key.pem" /grant:r "$($env:USERNAME):(R)"

```


2. Connect to your EC2 instance (replace `<YOUR_PUBLIC_IP>` with your instance's Public IPv4 address):
```powershell
ssh -i "C:\Users\hp\Downloads\ec2-deploy-key.pem" ec2-user@<YOUR_PUBLIC_IP>

```


*(Type `yes` when prompted to confirm the host fingerprint).*
* *Verification:* Your terminal prompt changes to `[ec2-user@ip-... ~]$`.



### 4. Install Tools, Build, and Run the Maven Project on EC2

1. Install Git, Java 17 (Amazon Corretto), Maven, and Nginx:
```bash
sudo dnf update -y
sudo dnf install -y git java-17-amazon-corretto-devel maven nginx

```


* *Verification:* Running `git --version && mvn -version` outputs the installed versions without errors.


2. Clone the repository, build the JAR, and verify the console output:
```bash
git clone https://github.com/haleema91/javacseh.git
cd javacseh
mvn clean package
java -cp target/javamaven1-0.0.1-SNAPSHOT.jar com.example1.javamaven1.App

```


* *Verification:* Maven ends with `[INFO] BUILD SUCCESS` and Java prints `demo on java maven project!`.



### 5. Publish the Project Output Live via Nginx

1. Start the Nginx web server and write your Java project's output to the web root:
```bash
sudo systemctl enable --now nginx
echo "<h1>Cloud Deployment on AWS EC2</h1><p>$(java -cp target/javamaven1-0.0.1-SNAPSHOT.jar com.example1.javamaven1.App)</p>" | sudo tee /usr/share/nginx/html/index.html

```


* *Verification:* Running `sudo systemctl is-active nginx` prints `active`.


2. Open a browser and visit `http://<YOUR_PUBLIC_IP>` (using `http://`, not `https://`).
* *Verification:* The browser loads your live page displaying **Cloud Deployment on AWS EC2** and **demo on java maven project!**.



### 6. Capture Evidence and End the Lab

1. Take screenshots of your running EC2 instance dashboard, your SSH terminal output, and the live browser page at `http://<YOUR_PUBLIC_IP>`.
2. Return to the **AWS Academy Learner Lab** tab and click **End Lab** so your lab credits are not drained while idle.
* *Verification:* The **AWS** indicator dot changes from green to red/grey.



##K8s


# Create deployment
kubectl create deployment mynginx --image=nginx

# Check pods
kubectl get pods

# Scale to 3
kubectl scale deployment mynginx --replicas=3

# Scale to 5
kubectl scale deployment mynginx --replicas=5

# Scale down to 2
kubectl scale deployment mynginx --replicas=2

# Verify
kubectl get deployments
kubectl get pods
