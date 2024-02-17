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
      stage ("audit packages: npm"){
        steps{
            sh "npm audit"
        }
      }

      stage('OWASP FS SCAN: owasp') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'dp-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('TRIVY FS SCAN: trivy') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
    
       stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build --build-arg TMDB_V3_API_KEY=Aj7ay86fe14eca3e76869b92 -t netflix ."
                       sh "docker tag netflix sevenajay/netflix:latest "
                       sh "docker push sevenajay/netflix:latest "
                    }
                }
            }
        }
        
        stage("TRIVY"){
            steps{
                sh "trivy image sevenajay/netflix:latest > trivyimage.txt" 
            }
        }

     } 
}