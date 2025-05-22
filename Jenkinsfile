Pipeline{
       agent {
             label 'AGENT-1'
       }
       option {
            timeout(time: 30, unit: 'MINUTES')
            disableConcurrentBuilds()
        }
        stages{
           stage ('build') {
             step {
                    sh 'echo this is build'
               }
            }
           stage ('test') {
             step {
                  sh 'echo this is test'
                  sh 'sleep 10'
                }
             }
          stage ('deploy'){
            step {
              sh 'echo this is deploy'
            }
          }       
    }
}
