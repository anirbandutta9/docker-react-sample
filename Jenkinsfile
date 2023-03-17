pipeline {
  agent {label 'batman'}
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub_access_token')
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t anirban9/react-k8s-app .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker push anirban9/react-k8s-app'
      }
    }
   stage('Deploy to GKE') {

   steps{
         step([$class: 'KubernetesEngineBuilder', projectId: 'kubernetes-380604', clusterName: 'batman', location: 'us-central1-c', manifestPattern: './k8s/deployment.yaml', credentialsId: 'gke-cluster101' , verifyDeployments: true])
            }
   }
  }
}
