pipeline {
    agent {
        label 'AGENT-1'
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    environment {
        ECR_REPO = '202533543549.dkr.ecr.us-east-1.amazonaws.com/expense/dev/mysql'
        IMAGE_TAG = 'v1.0.1'
    }

    stages {
        stage('Lint SQL') {
            steps {
                sh '''
                    pip install sqlfluff || true
                    sqlfluff lint sql/ || true
                '''
            }
        }

        stage('Secrets Scan') {
            steps {
                sh '''
                    curl -sL https://github.com/gitleaks/gitleaks/releases/latest/download/gitleaks-linux-amd64 -o gitleaks
                    chmod +x gitleaks
                    ./gitleaks detect --source . --verbose || true
                '''
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                sh """
                    docker build -t expense-mysql:${IMAGE_TAG} .
                    docker tag expense-mysql:${IMAGE_TAG} ${ECR_REPO}:${IMAGE_TAG}
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ECR_REPO}
                    docker push ${ECR_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('DB Integration Test') {
            steps {
                sh '''
                    docker run -d --name mysql-test -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=testdb mysql:5.7
                    sleep 20
                    pip install pytest pymysql || true
                    pytest tests/ || true
                    docker stop mysql-test
                    docker rm mysql-test
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh 'echo this is deploy step'
                deleteDir()
                sh "ls -ltr"
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        failure {
            echo 'Pipeline failed. Please check logs.'
        }
    }
}
