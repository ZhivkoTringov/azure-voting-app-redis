pipeline {
   agent {
      label 'master'
   }

   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
      }
      stage('Docker Build') {
         steps {
            sh(script: 'docker compose build')
         }
      }
      stage('Start App') {
         steps {
            sh(script: 'docker compose up -d')
         }
      }
      stage('Run Tests') {
         steps {
            sh(script: 'pytest ./tests/test_sample.py')
         }
         post {
            success {
               echo "Tests passed"
            }
            failure {
               echo "Tests failed"
            }
         }
      }
      stage('Run Grype'){
         agent {label 'Node1'}
         steps {
            grypeScan autoInstall: false, repName: 'grypeReport_${JOB_NAME}_${BUILD_NUMBER}.txt', scanDest: 'registry:zhivkotringov/azure-voting-app:latest'
         }
         post {
            always {
               recordIssues(
                  tools: [anchore()],
                  aggregatingResults: true,
               )
            }
         }
      }
   }
   post {
      always {
         sh(script: 'docker compose down')
      }
   }
}
