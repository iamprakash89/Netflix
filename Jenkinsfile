pipeline{
     agent any
     
     tools{
        jdk 'jdk17'
        nodejs 'node16'
     }

     environment{
        SCANNER_HOME=tool 'sonar-scanner'
     }
    //  <!-- Git Checkout Stage -->
     stages{
        
     stage('Clean Work Space : Jenkins'){
            steps{
                cleanWs()
            }
        }

     stage('Git Checkout : GIT'){
            steps{
                git branch: 'main', url: 'https://github.com/iamprakash89/Netflix.git'
            }
        }

     stage("Sonarqube Analysis : SonarQube "){
            steps{
                withSonarQubeEnv('sonarqube') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                }
            }
        }

      stage("Quality Gate : SonarQube "){
            steps{
               waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-api'
            }
        }

      stage ("Install dependencies; npm"){
        steps{
            sh "npm install"
        }
      }

     } 
}