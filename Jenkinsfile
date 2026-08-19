pipeline {
    agent any

    tools {
        maven 'JDK17_Maven'
        jdk 'JDK17'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Must match the SonarQube Server Name in Jenkins System settings
                withSonarQubeEnv('SonarQubeServer') { 
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar'
                }
            }
        }
/*
        stage('Quality Gate') {
            steps {
                // Waits for SonarQube to finish processing analysis
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
*/
        stage('Package EAR') {
            steps {
                sh 'mvn package -DskipTests'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'app-ear/target/*.ear', fingerprint: true
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
