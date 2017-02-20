node('k8s-gradle') {

    stage('Checkout') {
        checkout scm
    }

    stage('Build and unit test') {
        sh 'gradle clean build'
        stash(name: 'deployable', includes: "build/libs/*")
    }

}

node('k8s-cf') {

    stage('Deploy to cloud foundry') {

        // Login
        withCredentials([[$class          : 'UsernamePasswordMultiBinding',
                          credentialsId   : 'cloud-foundry-meetup-user',
                          usernameVariable: 'CLOUD_FOUNDRY_USERNAME',
                          passwordVariable: 'CLOUD_FOUNDRY_PASSWORD']]) {
            sh "cf login " +
                    "-a \"https://api.eu-west-1.apps.msi.audi.com\" " +
                    "-o \"ABI-3 INNO\" " +
                    "-s \"meetup\" " +
                    "-u \"${env.CLOUD_FOUNDRY_USERNAME}\" " +
                    "-p \"${env.CLOUD_FOUNDRY_PASSWORD}\" "
        }

        // Prepare deployment
        unstash(name: 'deployable')

        String buildpack = 'https://github.com/cloudfoundry/java-buildpack.git#v3.10'
        String pathToDeployable = 'build/libs/carservice-1.0.0.jar'
        String domain = 'eu-west-1.apps.msi.audi.com'
        String appName = 'carservice'
        String appNameBlue = "$appName-blue"
        String hostName = 'meetup-carservice'
        String hostNameBlue = "$hostName-blue"

        // Push app to blue instance
        sh "cf push $appNameBlue -b $buildpack -p $pathToDeployable -n $hostNameBlue"

        // Execute tests etc.
        // ...
        sleep(10)

        // Map blue app to live instance
        sh "cf map-route $appNameBlue $domain -n $hostName"

        // Just to visualize the blue-green-deployment
        sleep(10)

        // Delete old app, rename blue to live
        sh "cf delete $appName -f"
        sh "cf rename $appNameBlue $appName"

        // Logout
        sh 'cf logout'
    }

}