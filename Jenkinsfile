node ('jnlp-slave') {

    checkout scm

    env.DOCKER_API_VERSION="1.24"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "192.168.129.111/library/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName
    sh "ln -s /root/.docker/dockercfg/dockercfg /home/jenkins/.dockercfg"

    stage "Build"
    
        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
    
    stage 'Confirm psuhed images'

        input 'Confirm?'

    stage "Push"

        sh "docker push ${imageName}"

    stage "Deploy"

        sh "sed 's#127.0.0.1:30400/hello-kenzan:latest#'$BUILDIMG'#' applications/hello-kenzan/k8s/deployment.yaml | kubectl apply -f -"
        sh "kubectl rollout status deployment/hello-kenzan"
}
