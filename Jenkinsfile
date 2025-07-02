pipeline {

  environment {
    dockerimagename = "dabundiz/react-app"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        //git 'https://github.com/dabundiz/jenkins-kubernetes-deployment.git'
        git branch: 'main', credentialsId: 'Github-token', url: 'https://github.com/dabundiz/jenkins-kubernetes-deployment.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying React.js container to Kubernetes') {
      //steps {
        //script {
        //  kubernetesDeploy(configs: "deployment.yaml", "service.yaml")
        //}
      //}
      steps {
        // Apply Kubernetes deployment using the Kubernetes service account
        withCredentials([string(credentialsId: '46511131-0eb5-4abf-a3d2-ec22a281e93b', variable: 'KUBE_SA_TOKEN')]) {
          sh 'kubectl apply -f "*.yaml" \
            --token=$KUBE_SA_TOKEN \
            --server=http://127.0.0.1:33959/ \
            --insecure-skip-tls-verify'
        //withKubeConfig([credentialsId: '46511131-0eb5-4abf-a3d2-ec22a281e93b', serverUrl: 'http://127.0.0.1:45903/']) {
          //sh 'kubectl apply -f kubernetes-deployment.yaml'
        }
      }
    }

  }

}
