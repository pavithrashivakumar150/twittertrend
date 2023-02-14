pipeline{
    agent{
        node{
            label "slave02"
        }
    }
    environment {
            PATH = "/opt/apache-maven-3.8.7/bin:$PATH"
    }
    stages{
        stage ("Clone the code"){
            steps{
                git branch: 'main', url: 'https://github.com/Bathalapalli-SaiRangaPavan/twittertrend.git'
            }
        }
        stage ("Build the code"){
            steps{
                sh 'mvn clean install'
            }
        }
        stage ("SonarQube Analysis"){
            environment {
                scannerHome = tool 'sonarscanner'
            }
            steps{
                withSonarQubeEnv('sonarscanner'){
                      sh "${scannerHome}/bin/sonar-scanner"
            
                }

            }
        
        }
       
    }
}
