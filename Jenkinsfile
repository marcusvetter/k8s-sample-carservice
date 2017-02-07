node('k8s-gradle') {

    stage('Checkout') {
        checkout scm
    }

    stage('Build and Unit Test') {
        sh 'gradle clean build'
    }

}