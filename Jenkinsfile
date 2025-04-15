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
      stage('Docker Push') {
         steps {
            echo "Running in $WORKSPACE"
            dir("$WORKSPACE/azure-vote") {
               script {
                  withDockerRegistry([credentialsId: 'dockerhub', url: '']) {
                     sh 'docker build -t zhivkotringov/azure-voting-app .'
                     sh 'docker push zhvikotringov/azure-voting-app'
                  }
               }
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
