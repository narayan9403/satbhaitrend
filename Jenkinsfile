pipeline {
    agent any

    tools {
    maven 'M2_HOME'
    jdk 'JAVA_HOMEs'
}

    stages {
        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
