pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }

    environment {
        IMAGE_NAME = 'kimheang68/react-jenkin'
        CONTAINER_NAME = 'my-container' // Specify the name of your container
    }

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
                // sh 'npm run build'
            }
        }
        stage('Test') {
            steps {
                // sh 'npm run test'
                echo "Test"
                sh "echo IMAGE_NAME is ${env.IMAGE_NAME}" 
            }
        }
        stage('Check for Existing Container') {
            steps {
                script {
                    def containerExists = sh(script: "docker ps -a --filter name=${env.CONTAINER_NAME} -q", returnStatus: true)
                    if (containerExists == 0) {
                        sh "docker rm ${env.CONTAINER_NAME}"
                    }
                }
            }
        }
        
        stage('Build Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-cred',
                        passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    script {
                        sh 'docker build -t ${env.IMAGE_NAME} .'
                        sh "echo \$PASS | docker login -u \$USER --password-stdin"
                        sh 'docker push ${env.IMAGE_NAME}'
                    }
                }
            }
        }
        stage ('Deploy') {
            steps {
                script {
                    sh 'docker run -p 3000:80 -d --name ${env.CONTAINER_NAME} ${env.IMAGE_NAME}'
                }
            }
        }
    }
}
