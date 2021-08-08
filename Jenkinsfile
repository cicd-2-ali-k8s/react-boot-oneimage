pipeline {

    agent {
        node {
            label 'master'
        }
    }

    stages {
        
        stage(' Unit Testing') {
            steps {
                sh """
                echo "Running Unit Tests"
                """
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                echo "build docker image"
                docker build . -t registry-vpc.cn-shanghai.aliyuncs.com/k8s-demo-vic/react-boot:0.1
                """
            }
        }

        stage('Publish Docker Image') {
            steps {
                sh """
                echo "login to registry"
                cat dockerpwd | docker login -u=vicwu_sh --password-stdin registry-vpc.cn-shanghai.aliyuncs.com
                echo "push Image"
                docker push registry-vpc.cn-shanghai.aliyuncs.com/k8s-demo-vic/react-boot:0.1
                """
            }
        }

        stage('Deploy to K8s') {
            steps {
                sh """
                echo "Deploy to k8s"
                kubctl apply -f all-in-one.yaml
                """
            }
        }

    }  
}