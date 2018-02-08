#!/usr/bin/groovy

/**
 this section of the pipeline executes on the master, which has a lot of useful variables that we can leverage to configure our pipeline
 **/
node (''){
    env.OCP_API_SERVER = "${env.OPENSHIFT_API_URL}"
    env.OCP_TOKEN = readFile('/var/run/secrets/kubernetes.io/serviceaccount/token').trim()
}

// Building on the master for this demo
node('') {

    // Bundles up the current directory and sends it to OpenShift to run the s2i.
    stage('Build Images') {
        sh "oc start-build ${env.APP_NAME} --follow"
    }


    // no user changes should be needed below this point
    stage ('Deploy to Dev') {
        openshiftTag (apiURL: "${env.OCP_API_SERVER}", authToken: "${env.OCP_TOKEN}", destStream: "${env.APP_NAME}", destTag: 'latest', destinationAuthToken: "${env.OCP_TOKEN}", destinationNamespace: "${env.NAMESPACE_DEV}", namespace: "${env.NAMESPACE_BUILD}", srcStream: "${env.APP_NAME}", srcTag: 'latest')

        openshiftVerifyDeployment (apiURL: "${env.OCP_API_SERVER}", authToken: "${env.OCP_TOKEN}", depCfg: "${env.APP_NAME}", namespace: "${env.NAMESPACE_DEV}", verifyReplicaCount: true)
    }

    stage ('Deploy to Demo') {
        input "Promote Application to Demo?"

        openshiftTag (apiURL: "${env.OCP_API_SERVER}", authToken: "${env.OCP_TOKEN}", destStream: "${env.APP_NAME}", destTag: 'latest', destinationAuthToken: "${env.OCP_TOKEN}", destinationNamespace: "${env.NAMESPACE_DEMO}", namespace: "${env.NAMESPACE_DEV}", srcStream: "${env.APP_NAME}", srcTag: 'latest')

        openshiftVerifyDeployment (apiURL: "${env.OCP_API_SERVER}", authToken: "${env.OCP_TOKEN}", depCfg: "${env.APP_NAME}", namespace: "${env.NAMESPACE_DEMO}", verifyReplicaCount: true)
    }

}
