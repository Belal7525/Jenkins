pipeline {
    agent any

    parameters {
        booleanParam(name: 'SKIP_STABILITY', defaultValue: false, description: 'Skip Code Stability Analysis')
        booleanParam(name: 'SKIP_QUALITY', defaultValue: false, description: 'Skip Code Quality Analysis')
        booleanParam(name: 'SKIP_COVERAGE', defaultValue: false, description: 'Skip Code Coverage Analysis')
    }

    environment {
        RECIPIENTS = "mohammadbelal1803551@gmail.com"
        ARTIFACT_DIR = "target"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Belal7525/Jenkins.git', branch: 'main'
            }
        }

        stage('Parallel Code Analysis') {
            parallel {
                stage('Code Stability') {
                    when {
                        not { expression { params.SKIP_STABILITY } }
                    }
                    steps {
                        echo "Running code stability checks..."
                        sh 'mvn clean verify'
                    }
                }

                stage('Code Quality Analysis') {
                    when {
                        not { expression { params.SKIP_QUALITY } }
                    }
                    steps {
                        echo "Running code quality analysis..."
                        sh 'mvn sonar:sonar'
                    }
                }

                stage('Code Coverage Analysis') {
                    when {
                        not { expression { params.SKIP_COVERAGE } }
                    }
                    steps {
                        echo "Running code coverage checks..."
                        sh 'mvn jacoco:report'
                    }
                }
            }
        }

        stage('Generate Reports') {
            steps {
                echo "Generating reports..."
                archiveArtifacts artifacts: "${ARTIFACT_DIR}/**", allowEmptyArchive: true
                junit '**/target/surefire-reports/*.xml'
            }
        }

        stage('Approval for Publication') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    input message: "Approve to publish artifact?", ok: "Publish"
                }
            }
        }

        stage('Publish Artifacts') {
            steps {
                echo "Publishing artifacts..."
                sh 'echo "Uploading artifacts..."'
            }
        }
    }

    post {
        success {
            echo "Pipeline succeeded"
            mail to: env.RECIPIENTS,
                 subject: "✅ Jenkins Build #${env.BUILD_NUMBER} Successful",
                 body: """<h3>Build Successful</h3>
<p>Build <b>#${env.BUILD_NUMBER}</b> succeeded for <b>${env.JOB_NAME}</b>.</p>
<p><a href="${env.BUILD_URL}">View Job</a></p>
""",
                 mimeType: 'text/html'
        }

        failure {
            echo "Pipeline failed"
            mail to: env.RECIPIENTS,
                 subject: "❌ Jenkins Build #${env.BUILD_NUMBER} Failed",
                 body: """<h3>Build Failed</h3>
<p>Build <b>#${env.BUILD_NUMBER}</b> failed for <b>${env.JOB_NAME}</b>.</p>
<p><a href="${env.BUILD_URL}">View Job</a></p>
""",
                 mimeType: 'text/html'
        }
    }
}
