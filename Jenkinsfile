pipeline {
  environment {
    registry = "azzu9394/jhooq-springboot-container"
    registryCredential = 'docker_credentials'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Gradle Build') {
      steps {
        git 'https://github.com/Azarazzu/springboot-with-docker.git'
        script{
              bat 'gradlew build'
        }
      }
    }
    stage('Building Docker Image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image To Docker Hub') {
      steps{
        script {
          /* Finally, we'll push the image with two tags:
                   * First, the incremental build number from Jenkins
                   * Second, the 'latest' tag.
                   * Pushing multiple tags is cheap, as all the layers are reused. */
          docker.withRegistry('https://registry.hub.docker.com', 'docker_credentials') {
              dockerImage.push("${env.BUILD_NUMBER}")
              dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy to Kubernetes'){
        steps{
          withKubeConfig([credentialsId: 'mykubeconfig', serverUrl: '']) {
            powershell 'kubectl apply -f k8s-spring-boot-deployment.yml'
            powershell 'kubectl apply -f service.yml'
            powershell 'kubectl rollout restart deployment jhooq-springboot'
            
       }
        }
    }
  }
}
