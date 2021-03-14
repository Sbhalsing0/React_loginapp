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
            steps {
                withAWS(region:'us-east-1',credentials:'aws') {
                    sh 'aws eks --region us-east-1 update-kubeconfig --name sanket_eks'                    
                }
            }
        }
        stage('Deploy Updated Image to Cluster'){
            steps {
                sh '''
                    export IMAGE="$registry:$BUILD_NUMBER"
                    sed -ie "s~IMAGE~$IMAGE~g" kubernetes/container.yml
                    kubectl apply -f ./kubernetes
            }
        }
    }
}
