node {
  def project = 'guestbook-kubernetes'
  def appName = 'php-redis'
  def feSvcName = "${appName}-frontend"
  def imageTag = "${project}/${appName}:${env.BUILD_NUMBER}"
  def registryTag = "rdoregistry.azurecr.io/${project}/${appName}:${env.BUILD_NUMBER}"

  checkout scm

  stage 'Deploy Redis Master Deployment and Service'
  sh("kubectl create -f redis-master-deployment.yaml")
  sh("kubectl create -f redis-master-service.yaml")

  stage 'Deploy Redis Slave Deployment and Service'
  sh("kubectl create -f redis-slave-deployment.yaml")
  sh("kubectl create -f redis-slave-service.yaml")

  stage 'Build Frontend PHP Image'
  sh("docker build -t ${imageTag} ${appName}")

  //stage 'Run Go tests'
  //sh("docker run ${imageTag} go test")

  stage 'Register Frontend PHP Image ACR'
  //sh("gcloud docker push ${imageTag}")
  sh("docker login rdoregistry.azurecr.io -u rdoregistry -p ${ACS_REGISTRY}")
  sh("docker tag ${imageTag} ${registryTag}")
  sh("docker push ${registryTag}")

  stage 'Deploy Frontend PHP Image Deployment and Service'
  sh("kubectl create -f rdo-frontend-deployment.yaml")
  sh("kubectl create -f frontend-service.yaml")

  //stage "Deploy Application"
  //switch (env.BRANCH_NAME) {
    // Roll out to canary environment
    //case "canary":
        // Change deployed image in canary to the one we just built
      //  sh("sed -i.bak 's#gcr.io/cloud-solutions-images/gceme:1.0.0#${imageTag}#' ./k8s/canary/*.yaml")
      //  sh("kubectl --namespace=production apply -f k8s/services/")
      //  sh("kubectl --namespace=production apply -f k8s/canary/")
      //  sh("echo http://`kubectl --namespace=production get service/${feSvcName} --output=json | jq -r '.status.loadBalancer.ingress[0].ip'` > ${feSvcName}")
      //  break

    // Roll out to production
    //case "master":
        // Change deployed image in canary to the one we just built
       // sh("sed -i.bak 's#gcr.io/cloud-solutions-images/gceme:1.0.0#${imageTag}#' ./k8s/production/*.yaml")
       /// sh("kubectl --namespace=production apply -f k8s/services/")
       // sh("kubectl --namespace=production apply -f k8s/production/")
       // sh("echo http://`kubectl --namespace=production get service/${feSvcName} --output=json | jq -r '.status.loadBalancer.ingress[0].ip'` > ${feSvcName}")
      //  break

    // Roll out a dev environment
    //default:
        // Create namespace if it doesn't exist
      //  sh("kubectl get ns ${env.BRANCH_NAME} || kubectl create ns ${env.BRANCH_NAME}")
        // Don't use public load balancing for development branches
      //  sh("sed -i.bak 's#LoadBalancer#ClusterIP#' ./k8s/services/frontend.yaml")
       // sh("sed -i.bak 's#gcr.io/cloud-solutions-images/gceme:1.0.0#${imageTag}#' ./k8s/dev/*.yaml")
      //  sh("kubectl --namespace=${env.BRANCH_NAME} apply -f k8s/services/")
       // sh("kubectl --namespace=${env.BRANCH_NAME} apply -f k8s/dev/")
       // echo 'To access your environment run `kubectl proxy`'
       // echo "Then access your service via http://localhost:8001/api/v1/proxy/namespaces/${env.BRANCH_NAME}/services/${feSvcName}:80/"
  //}
}