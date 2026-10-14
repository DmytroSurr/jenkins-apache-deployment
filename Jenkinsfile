
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
                    java -version
                    git --version
                '''
            }
        }

        stage('Deploy Apache') {
            steps {
                sh '''
                    set -eu
                    echo "Installing and configuring Apache..."
                    sudo -n /usr/local/sbin/deploy-apache.sh
                '''
            }
        }

        stage('Verify Apache Service') {
            steps {
                sh '''
                    set -eu
                    systemctl is-active --quiet apache2
                    apache2 -v
                    echo "Apache service is active."
                '''
            }
        }

        stage('HTTP Smoke Test') {
            steps {
                sh '''
                    set -eu

                    for attempt in 1 2 3 4 5; do
                        if curl --fail --silent --show-error \
                            http://127.0.0.1/; then
                            echo
                            echo "HTTP smoke test passed."
                            exit 0
                        fi

                        echo "Retry ${attempt}/5..."
                        sleep 2
                    done

                    echo "HTTP smoke test failed."
                    exit 1
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Apache deployment and health checks passed.'
        }
        failure {
            echo 'FAILURE: Check the stage logs for details.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
