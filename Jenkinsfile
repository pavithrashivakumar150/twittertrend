def registry = 'https://pavithramohan143.jfrog.io'
def imageName = 'pavithramohan143.jfrog.io/valaxy-docker-docker-loca/image'
   def version   = '2.0.2'
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
                sh 'mvn clean deploy'
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
      stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'jfrogid'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }
        
    }
}

