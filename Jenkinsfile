pipeline {
    agent any
    environment {
        registry = "sbhalsing0/reactapp"
        registryCredential = 'dockerhub'
        dockerImage = ''
        IMAGE="${env.BUILD_NUMBER}"
    }
    stages {
         stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Upload Image to Docker hub') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Remove Unused docker image') {
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
        stage('Deploy Updated Image to Cluster'){
            steps {
                sh '''
                    echo "HELLO"
                    '''
            }
        }
        stage('Update Kube Config'){
        steps{
          echo "Deploy on k8s"

          kubernetesDeploy(
            kubeconfigId: 'kube_config',
            configs: 'kubernetes/Deployment.yml',
            enableConfigSubstitution: true
          )
        }
     }
  }
}
