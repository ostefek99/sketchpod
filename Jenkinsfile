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

    stage ('Security Scanning')
    {
        echo 'Security Scanning... please wait'
        sleep 3
    }

    // no user changes should be needed below this point
    stage ('Deploy to Dev') {
        sh "oc tag ${env.NAMESPACE_DEV}/${env.APP_NAME}:latest ${env.NAMESPACE_TEST}/${env.APP_NAME}:latest"
    }

    stage ('Deploy to Demo') {
        input "Promote Application to Demo?"
        sh "oc tag ${env.NAMESPACE_DEMO}/${env.APP_NAME}:latest ${env.NAMESPACE_TEST}/${env.APP_NAME}:latest"
    }
}
