node {
  def project = 'guestbook-kubernetes'
  def appName = 'php-redis'
  def feSvcName = "${appName}-frontend"
  def latestTag = "${project}/${appName}:latest" 
  def imageTag = "${project}/${appName}:${env.BUILD_NUMBER}"
  def registryTag = "rdoregistry.azurecr.io/${project}/${appName}:${env.BUILD_NUMBER}"
  def registryLatestTag = "rdoregistry.azurecr.io/${project}/${appName}:latest"

  checkout scm

  stage 'Deploy Redis Master Deployment and Service'
  sh("kubectl delete deployment -l app=redis --kubeconfig /home/rdoadmin/apps/jenkins/config")
  sh("kubectl delete service -l app=redis --kubeconfig /home/rdoadmin/apps/jenkins/config")
  sh("kubectl create -f redis-master-deployment.yaml --kubeconfig /home/rdoadmin/apps/jenkins/config")
  sh("kubectl create -f redis-master-service.yaml --kubeconfig /home/rdoadmin/apps/jenkins/config")

  stage 'Deploy Redis Slave Deployment and Service'
  sh("kubectl create -f redis-slave-deployment.yaml --kubeconfig /home/rdoadmin/apps/jenkins/config")
  sh("kubectl create -f redis-slave-service.yaml --kubeconfig /home/rdoadmin/apps/jenkins/config")

  stage 'Build Frontend PHP Image'
  sh("docker build -t ${imageTag} ${appName}")

  //stage 'Run Go tests'
  //sh("docker run ${imageTag} go test")

  stage 'Register Frontend PHP Image ACR'
  //sh("gcloud docker push ${imageTag}")
  sh("docker login rdoregistry.azurecr.io -u rdoregistry -p ${ACS_REGISTRY}")
  sh("docker tag ${imageTag} ${registryLatestTag}")
  sh("docker push ${registryLatestTag}")

  stage 'Deploy Frontend PHP Image Deployment and Service'
  sh("kubectl delete deployment -l app=guestbook --kubeconfig /home/rdoadmin/apps/jenkins/config")
  sh("kubectl delete service -l app=guestbook --kubeconfig /home/rdoadmin/apps/jenkins/config")
  sh("kubectl create -f rdo-frontend-deployment.yaml --kubeconfig /home/rdoadmin/apps/jenkins/config")
  sh("kubectl create -f rdo-frontend-service.yaml --kubeconfig /home/rdoadmin/apps/jenkins/config")

  
}