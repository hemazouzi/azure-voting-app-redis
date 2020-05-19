pipeline{
    agent any
     stages{
        
        stage('Verify Branch'){
             steps{
                 echo "$GIT_BRANCH"
             }
        }

        stage('Docker Build'){
            steps{
                sh ' docker images -a'
                sh label: '', script: '''
                    cd azure-vote/
                    docker images -a
                    docker build -t jenkins-pipeline .
                    docker images -a
                    cd ..
                '''
            }
        }

        stage('Start test app') {
            steps {
                sh label: '', script: '''
                docker-compose up -d
                '''
            }
            post {
                success {
                    echo "App started successfully :)"
                }
                failure {
                    echo "App failed to start :("
                }
            }
        }

        stage('Run Tests') {
         steps {
            sh label: '', script: '''
               pytest ./tests/test_sample.py
            '''
         }
      }

      stage('Stop test app') {
         steps {
            sh label: '', script: '''
               docker-compose down
            '''
         }
      }

    stage ('Push Container'){
          steps{
              echo "The workspace is $WORKSPACE"
              dir("$WORKSPACE/azure-vote"){
                  script{
                        docker.withRegistry('', 'dockerHub'){
                          def image = docker.build('hemazouzi/jenkins-he:latest')
                          image.push()
                      }
                  }
              }
          }
    }
    
    stage('Run Trivy') {
        steps {
            sleep(time: 30, unit: 'SECONDS')
                sh label: '', script: '''
                    trivy hemazouzi/jenkins-he
                '''
            }
        }
        
     }
}