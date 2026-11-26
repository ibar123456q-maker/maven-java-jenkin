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
