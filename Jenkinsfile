pipeline {
  environment {
    registry = "azzu9394/jhooq-docker-demo:jhooq-docker-demo"
    registryCredential = 'docker_credentials'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Compile',) {
      steps {
        git 'https://github.com/Azarazzu/springboot-with-docker.git'
         powershell './gradlew build'        
        }
      }
    }
    stage("Docker build"){
        powershell 'docker version'
        powershell 'docker build -t jhooq-docker-demo .'
        powershell 'docker image list'
        powershell 'docker tag jhooq-docker-demo azzu9394/jhooq-docker-demo:jhooq-docker-demo'
    } 
    stage("Push Image to Docker Hub"){
        powershell 'docker push azzu9394/jhooq-docker-demo:jhooq-docker-demo'
    }

    stage('Deploy to Kubernetes'){
        steps{
              withKubeConfig([credentialsId: 'mykubeconfig', serverUrl: '']) {
            powershell 'kubectl apply -f k8s-spring-boot-deployment.yml'
       }
        }
    }
  }
}
