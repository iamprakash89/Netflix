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
                       sh "docker build --build-arg TMDB_V3_API_KEY=af69cebb7819fa4c5378c74fbbb2ffb5 -t netflix ."
                       sh "docker tag netflix devopskvk/netflix:latest "
                       sh "docker push devopskvk/netflix:latest "
                    }
                }
            }
        }

        stage("TRIVY"){
            steps{
                sh "trivy image devopskvk/netflix:latest > trivyimage.txt" 
            }
        }

        stage('Deploy to container'){
            steps{
                sh 'docker run -d --name netflix -p 8081:80 devopskvk/netflix:latest'
            }
        }


     } 
}