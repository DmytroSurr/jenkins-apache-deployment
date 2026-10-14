
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {
        stage('Check Environment') {
            steps {
                sh '''
                    set -eu
                    echo "Checking Ubuntu environment..."
                    cat /etc/os-release
                    command -v apt-get
                    command -v systemctl
                    command -v curl
                '''
            }
        }

        stage('Install Apache') {
            steps {
                echo 'Apache installation will run after deployment permissions are configured.'
                sh '''
                    set -eu
                    if ! command -v apache2 >/dev/null 2>&1; then
                        echo "Apache is not installed yet."
                        echo "Deployment permissions must be configured before installation."
                        exit 1
                    fi
                    apache2 -v
                '''
            }
        }

        stage('Check Apache Service') {
            steps {
                sh '''
                    set -eu
                    systemctl is-active --quiet apache2
                    echo "Apache service is active."
                '''
            }
        }

        stage('HTTP Smoke Test') {
            steps {
                sh '''
                    set -eu
                    for attempt in 1 2 3 4 5; do
                        if curl --fail --silent --show-error http://127.0.0.1/; then
                            echo
                            echo "HTTP smoke test passed."
                            exit 0
                        fi
                        echo "Waiting for Apache... attempt ${attempt}/5"
                        sleep 2
                    done
                    echo "Apache HTTP smoke test failed."
                    exit 1
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment checks completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Review the stage logs above.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
