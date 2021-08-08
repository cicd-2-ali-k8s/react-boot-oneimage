pipeline {

    agent {
        node {
            label 'master'
        }
    }

    stages {
        
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
                sh """
                echo "Cleaned Up Workspace For Project"
                """
            }
        }

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
                echo "Running Code Analysis"
                docker build . -t registry-vpc.cn-shanghai.aliyuncs.com/k8s-demo-vic/react-boot:0.1
                """
            }
        }

        stage('Deploy Image') {
            steps {
                sh """
                echo "Building Artifact Docker"
                """

                sh """
                echo "Deploying Code"
                """
            }
        }

    }  
}