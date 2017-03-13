node('k8s-gradle') {
  
  stage('Checkout') {
    checkout scm
  }
  
  stage('Build and unit test') {
    sh 'gradle clean build'
  }
  
}
