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
                echo "docker build . -t registry-vpc.cn-shanghai.aliyuncs.com/k8s-demo-vic/react-boot:0.1"
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
                sh '''
                echo "Deploy to k8s"
                echo "kubectl apply -f all-in-one.yaml"
                '''
            }
        }

    }  
}