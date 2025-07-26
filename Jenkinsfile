pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Belal7525/Jenkins.git', branch: 'main'
            }
        }

        stage('Code Stability') {
            steps {
                echo "Running Code Stability..."
                sh 'mvn clean verify'
            }
        }

        stage('Code Quality') {
            steps {
                echo "Running Code Quality Scan..."
                sh 'mvn checkstyle:check'
            }
        }

        stage('Code Coverage') {
            steps {
                echo "Running Code Coverage Analysis..."
                sh 'mvn jacoco:report'
            }
        }

        stage('Generate Reports') {
            steps {
                echo "Generating Reports..."
                sh 'cp -r target /tmp/artifacts'
            }
        }

        stage('Approval') {
            steps {
                input message: 'Approve to publish artifacts?', ok: 'Approve'
            }
        }

        stage('Publish Artifacts') {
            steps {
                echo "Publishing Artifacts..."
                sh 'sudo cp -r /tmp/artifacts /var/www/html/java-report'
            }
        }
    }

    post {
        success {
            mail to: 'mohammadbelal1803551@gmail.com',
                 subject: "SUCCESS: Jenkins Job '${env.JOB_NAME}' (#${env.BUILD_NUMBER})",
                 body: "Jenkins job succeeded!\nCheck it here: ${env.BUILD_URL}"
        }
        failure {
            mail to: 'mohammadbelal1803551@gmail.com',
                 subject: "FAILURE: Jenkins Job '${env.JOB_NAME}' (#${env.BUILD_NUMBER})",
                 body: "Jenkins job failed!\nCheck it here: ${env.BUILD_URL}"
        }
    }
}
