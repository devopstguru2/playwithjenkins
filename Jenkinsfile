pipeline {

  environment {
    registry = "ishwary/devops"
    registryCredential = 'dockerhub'
    dockerImage = ""
    
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        checkout scm
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }

    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "", registryCredential) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy to GKE') {
      steps {
         sh "sed -i 's/latest/${env.BUILD_ID}/g' mywebservice.yaml"
         
        step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME_TEST, location: env.LOCATION, manifestPattern: 'mywebservice.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
      }
    }

  }

}
