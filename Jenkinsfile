pipeline {

    agent {
        node {
            label 'master'
        }
    }

    stages {
        
        stage(' Unit Testing') {
            steps {
                sh '''
                echo "Running Unit Tests"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "build docker image"
                docker build . -t registry-vpc.cn-shanghai.aliyuncs.com/k8s-demo-vic/react-boot:0.1
                '''
            }
        }

        stage('Publish Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-login', 
                            usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                    echo "login to registry"
                    docker login -u=${USERNAME} --password=${PASSWORD} registry-vpc.cn-shanghai.aliyuncs.com
                    echo "push Image"
                    docker push registry-vpc.cn-shanghai.aliyuncs.com/k8s-demo-vic/react-boot:0.1
                    '''
                }
            }
        }

        stage('Deploy to K8s') {
            steps {
                withCredentials([file(credentialsId: 'kubectl.config', variable: 'KUBECONFIG')]) {
                    sh '''
                    echo "Deploy to k8s"
                    /usr/local/bin/kubectl apply -f all-in-one.yaml
                    '''
                }
            }
        }

    }  
}