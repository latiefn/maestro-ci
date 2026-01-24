pipeline {
    agent any

    environment {
        ADB_HOST = "host.docker.internal"
        ADB_PORT = "5555"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('ADB Connect') {
            steps {
                sh '''
                    adb kill-server || true
                    adb start-server
                    adb connect ${ADB_HOST}:${ADB_PORT} || true
                    adb devices
                '''
            }
        }

        stage('Run Maestro') {
            steps {
                sh '''
                    mkdir -p .maestro/debug
                    maestro test flows \
                      --debug-output .maestro/debug \
                      --format junit \
                      --output maestro-report.xml
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '.maestro/**', fingerprint: true
            archiveArtifacts artifacts: 'maestro-report.xml', fingerprint: true
        }
        failure {
            echo "❌ Maestro test failed"
        }
    }
}
