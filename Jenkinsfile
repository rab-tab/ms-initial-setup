node{
    stage('Checkout'){
        checkout([$class: 'GitSCM',
        branches: [[name: '*/main']],
        extensions: [],
        userRemoteConfigs :[[credentialsId : 'git',
        url : 'https://github.com/rab-tab/ms-initial-setup.git']]])
    }
    stage('Build and Push Image'){
        withCredentials([file(credentialsId: 'gcp', variable: 'GC_KEY')]) {
            sh("gcloud auth activate-service-account --key-file=${GC_KEY}")
            sh 'gcloud auth configure-docker us-west4-docker.pkg.dev'
            sh "${mvnCMD} clean install jib:build -DREPO_URL=${REGISTRY_URL}/${PROJECT_ID}/${ARTIFACT_REGISTRY}"
        }
     }
    stage('Deploy'){
        step([$class: 'KubernetesEngineBuilder',
                projectId: env.PROJECT_ID,
                clusterName: env.CLUSTER,
                location: env.ZONE,
                manifestPattern: 'k8s/',
                credentialsId: env.PROJECT_ID,
                verifyDeployments: true])
    }
}