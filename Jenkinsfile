pipeline {
    agent any

    tools {
        // Must match the tool name configured in Jenkins Global Tool Configuration
        maven 'JDK17_Maven'
        jdk 'JDK17'
    }

    environment {
        APP_NAME = 'enterprise-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'feature-branch', url: 'https://github.com/bus57790/maven-enterprise-app.git'
            }
        }
        stage('Compile & Static Analysis') {
            steps {
                echo 'Compiling Java sources across sub-modules...'
                sh 'mvn clean compile'
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
            post {
                always {
                    // Archive test results in Jenkins UI
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package EAR') {
            steps {
                echo 'Packaging Enterprise Application (EAR)...'
                sh 'mvn package -DskipTests'
            }
            post {
                success {
                    // Archive the generated EAR artifact
                    archiveArtifacts artifacts: 'app-ear/target/*.ear', fingerprint: true
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
            cleanWs() // Clean workspace after build completion
        }
        failure {
            echo 'Pipeline failed. Check build logs.'
        }
    }
}
