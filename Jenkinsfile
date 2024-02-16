pipeline{
     agent any
     
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

     stage('Sonar qube Analysis : SonarQube'){
            steps{
                sh 'mvn clean package sonar:sonar'
            }
        }

     } 
}