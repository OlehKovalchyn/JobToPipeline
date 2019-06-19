
pipeline {
    agent any 
    stages {
        stage('Checkout git repo') {
        deleteDir()
        git (
            branch: 'master',
            credentialsId: '3c466005-22b4-4fd5-af67-04b93c9889d9',
            url: 'https://portal-ua.globallogic.com/gitlab/mbv-coros-cloud/coros_lwm2m_dc.git'
            )
          }
              stage('Test') {
          try {
            sh "mvn clean test -e"
        } catch (err) {

        } finally {
            junit testResults: 'target/surefire-reports/*.xml', allowEmptyResults: true
        }
       }
           stage('Results') {
            junit '**/target/surefire-reports/*.xml'
   }

    stage('Build Project') {
            sh 'mvn clean install'
          }

    stage('Building image') {
            sh 'sudo docker build -t mbvdockerregistry.azurecr.io/dc-server:prod .'
        }

    stage('Push Image') {
        sh 'docker login -u ${DOCKER_USER} -p ${DOCKER_PASS} mbvdockerregistry.azurecr.io';
        sh 'sudo docker push mbvdockerregistry.azurecr.io/dc-server:prod'
    }

     stage('Deploy to k8s') {
        sh 'kubectl set image deployments/mbv-dc-server mbv-dc-server=mbvdockerregistry.azurecr.io/dc-server:prod --kubeconfig /var/lib/jenkins/config'
  }
              
 }
}
