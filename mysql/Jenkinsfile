pipeline{
    agent {
        label'AGENT-1'
    }
    option {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    environment{
        def appVersion = '' //variable declaration
        nexusUrl = 'nexus.anuprasad.online:8081'
        region = "us-east-1"
        account_id = "202533543549"
    }
    stages {
        stage ('build'){
            steps{
                sh """
                aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${account_id}.dkr.ecr.${region}.amazonaws.com

                docker build -t ${account_id}.dkr.ecr.${region}.amazonaws.com/expense-backend:${appVersion} .

                docker push ${account_id}.dkr.ecr.${region}.amazonaws.com/expense-backend:${appVersion}

                """
            }
        }
    }
}