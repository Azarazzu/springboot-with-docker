pipeline {
  environment {
    registry = "azzu9394/springboot-with-docker"
    registryCredential = 'docker_credentials'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Compile') {
      steps {
        git 'https://github.com/Azarazzu/springboot-with-docker.git'
        script{
                def gradelHome = tool name: 'GRADLE_HOME', type: 'gradle'
                bat "${gradleHome}/bin/gradle bootrun"
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
          docker.withRegistry('https://hub.docker.com/', 'docker_credentials') {
              dockerImage.push("${env.BUILD_NUMBER}")
              dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy to Kubernetes'){
        steps{
              withKubeConfig([credentialsId: 'mykubeconfig']) {
          powershell '''SET PATH=C:\\Program Files (x86)\\Google\\Cloud SDK\\google-cloud-sdk\\bin
kubectl apply -f deployment.yml'''
       }
        }
    }
  }
}
