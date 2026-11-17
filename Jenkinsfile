pipeline {
 agent any
 environment {
    IMAGE = "anoncat01/myapp-ci"
 }
 stages {
    stage('Checkout') {
        steps { checkout scm }
 }
    stage('Build Image') {
        steps { sh 'docker build -t $IMAGE:${BUILD_NUMBER} -t $IMAGE:latest .'}
 }
    stage('Test') {
        steps {sh 'docker run --rm $IMAGE:${BUILD_NUMBER} npm test'}
 }
    stage('Push to Docker Hub'){
        steps{
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds' ,
        usernameVariable: 'USER', passwordVariable: 'PASS')]){
            sh '''
              echo $PASS | docker login -u $USER --password-stdin
              docker push $IMAGE:${BUILD_NUMBER}
              docker push $IMAGE:latest
              '''
        }
    }
 }
    stage('Deploy'){
        steps {
        sh '''
          docker rm -f myapp || true
          docker run -d --name myapp -p 3000:3000 $IMAGE:latest
          '''
    }
 }
}
}
