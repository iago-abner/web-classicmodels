pipeline {
    agent any
    stages {
        stage('Build Docker image - frontend') {
            steps {
                script {
                    dockerfront = docker.build("iagoabner/frontend-classicmodels:${env.BUILD_ID}", "-f ./dockerfile .")
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
        stage ('Update version in deployment file k8s') {
          environment {
              tag_version = "${env.BUILD_ID}"
          }
          steps {
              withKubeConfig([credentialsId: 'kubeconfig']) {
                  sh 'sed -i "s/{{tag}}/$tag_version/g" /opt/k8s/deployment.yml'
              }
          }
      }
    }
}
