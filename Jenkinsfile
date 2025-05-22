pipeline {
    agent {
        label 'AGENT-1'
    }

    environment {
        ECR_REGISTRY = '202533543549.dkr.ecr.us-east-1.amazonaws.com'
        ECR_REPO = "${ECR_REGISTRY}/expense/dev/mysql"
        IMAGE_TAG = 'v1.0.1'
        IMAGE = "${ECR_REPO}:${IMAGE_TAG}"
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    stages {

        stage('Secret Scan (Gitleaks)') {
            steps {
                sh '''
                echo "🔐 Running Gitleaks scan..."
                docker run --rm -v $(pwd):/repo zricethezav/gitleaks detect --source=/repo --no-git -v
                '''
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                sh '''
                echo "🔧 Building Docker image..."
                docker build -t expense-mysql:${IMAGE_TAG} .

                echo "🔄 Tagging image..."
                docker tag expense-mysql:${IMAGE_TAG} ${IMAGE}

                echo "🔐 Logging into AWS ECR..."
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ECR_REGISTRY}

                echo "📤 Pushing image to ECR..."
                docker push ${IMAGE}
                '''
            }
        }

        stage('Scan Docker Image (Trivy)') {
            steps {
                sh '''
                echo "🛡️ Scanning Docker image with Trivy..."
                docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --exit-code 0 --severity HIGH,CRITICAL ${IMAGE}
                '''
            }
        }

        stage('Unit Test Placeholder') {
            steps {
                sh '''
                echo "🧪 Running placeholder tests..."
                sleep 5
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                echo "🚀 Deploying to EKS..."
                aws eks update-kubeconfig --region us-east-1 --name roboshop
                kubectl apply -f manifest.yaml
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully."
        }
        failure {
            echo "❌ Pipeline failed — check the logs."
        }
        always {
            echo "📋 Cleaning up (if needed)..."
        }
    }
}
