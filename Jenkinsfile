def registry = 'https://pavithramohan143.jfrog.io'	
def imageName = 'pavithramohan143.jfrog.io/twitter-docker-local/image'    
def version   = '2.0.2'
pipeline{
    agent {
        node {
            label "docker_node"
        }
    }
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }
    stages {
        stage ("Clone the code"){
            steps{
                git branch: 'main', url: 'https://github.com/pavithrashivakumar150/twittertrend.git'
            }
        }
        stage('build') {
            steps{
                echo "------------ build started ---------"
                sh 'mvn clean deploy'
                echo "------------ build completed ---------"
        }
      }
        stage("Code analysis"){
            environment{
                scannerHome = tool "sonarscanner01"
            }
            steps{
                echo '<--------------- Sonar Analysis started  --------------->'
                withSonarQubeEnv('sonarserver'){
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
        stage(" Docker Build ") {
            steps {
                script {
                    echo '<--------------- Docker Build Started --------------->'
                    app = docker.build(imageName+":"+version)
                    echo '<--------------- Docker Build Ends --------------->'
                }
            }
        }
        stage (" Docker Publish ") {
            steps {
                script {
                    echo '<--------------- Docker Publish Started --------------->'
                    docker.withRegistry(registry, 'jfrogid') {
                         app.push()
                    }
                }
            }
        }      
    }
}
