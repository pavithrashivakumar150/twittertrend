def registry = 'https://pavithramohan143.jfrog.io'
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
                git branch: 'main', url: 'https://github.com/pavithrashivakumar150/twittertrend.git'
            }
        }
        stage ("Build the code"){
            steps{
                sh 'mvn clean install'
            }
        }
        stage ("SonarQube Analysis"){
            environment {
                scannerHome = tool 'sonarscanner01'
            }
            steps{
                withSonarQubeEnv('Java_SonarQube_Server'){
                      sh "${scannerHome}/bin/sonar-scanner"
            
                }

            }
        
        }
        
       stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrogid"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "jenkinsjfrog-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }   
    }
}

