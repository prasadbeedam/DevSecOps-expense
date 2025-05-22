pipeline {
    agent {
        label 'AGENT-1'
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    stages {
        stage('build') {
            steps {
                sh """
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 202533543549.dkr.ecr.us-east-1.amazonaws.com
                docker build -t 202533543549.dkr.ecr.us-east-1.amazonaws.com/expense/dev/mysql:v1.0.1 .
                docker push 202533543.dkr.ecr.us-east-1.amazonaws.com/expense/dev/mysql:v1.0.1
                """
            }
        }

        stage('test') {
            steps {
                sh 'echo this is test'
                sh 'sleep 10'
            }
        }

        stage('deploy') {
            steps {
                sh 'echo this is deploy'
            }
        }
        post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'I will run when pipeline is success'
        }
        failure { 
            echo 'I will run when pipeline is failure'
        }
    }
}
