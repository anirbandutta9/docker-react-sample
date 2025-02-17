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
        sh 'docker build -t anirban9/react-k8s-app:$BUILD_ID . '
        sh 'docker tag anirban9/react-k8s-app:$BUILD_ID anirban9/react-k8s-app:latest'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker push anirban9/react-k8s-app:$BUILD_ID '
        sh 'docker push anirban9/react-k8s-app:latest '
      }
    }
    stage('Deploy locally') {
      steps {
        sh '''docker stop $(docker ps -a -q)
             docker rm $(docker ps -a -q)
             docker pull anirban9/react-k8s-app:$BUILD_ID
             docker run -p 80:80 -d anirban9/react-k8s-app:$BUILD_ID '''
      }
    }

   stage('Deploy to GKE') {
   steps{
         sh "sed -i 's/react-k8s-app:latest/react-k8s-app:$BUILD_ID/g' ./k8s/deployment.yaml"
         step([$class: 'KubernetesEngineBuilder', projectId: 'kubernetes-380604', clusterName: 'batman', location: 'us-central1-c', manifestPattern: './k8s/', credentialsId: 'gke-cluster101' , verifyDeployments: true])
            }
   }
  }
}
