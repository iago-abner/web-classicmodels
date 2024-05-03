pipeline {
    agent any
    stages {
        stage('Build Docker image - frontend') {
            steps {
                dir('frontend') {
                    script {
                        dockerfront = docker.build("iagoabner/frontend-classicmodels:${env.BUILD_ID}", "-f ./dockerfile .")
                    }
                }
            }
        }
        stage ('Push frontend image - DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerfront.push('latest')
                        dockerfront.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage ('Deploy Kubernetes') {
          environment {
              tag_version = "${env.BUILD_ID}"
          }
          steps {
              withKubeConfig([credentialsId: 'kubeconfig']) {
                  sh 'ls -l /opt'
                  sh 'ls -l /opt/k8s'
                  sh 'sed -i "s/{{tag}}/$tag_version/g" /opt/k8s/deployment.yaml'
                  sh 'kubectl apply -f /opt/k8s/deployment.yaml'
              }
          }
      }
    }
}
