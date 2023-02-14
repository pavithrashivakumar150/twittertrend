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
                scannerHome = tool 'Java_SonarQube_Scanner'
            }
            steps{
                withSonarQubeEnv('Java_SonarQube_Server'){
                      sh "${scannerHome}/bin/sonar-scanner"
            
                }

            }
        
        }
        stage ("Quality Gate"){
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS'){
                        def qg = waitForQualityGate()
                        if(qg.status !='OK') {
                            error "Pipeline failed due to quality gate failures: ${qg.status}"
                        }
                    }
                }
            }
        }

    }
}
