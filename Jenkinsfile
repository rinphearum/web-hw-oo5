pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }

    environment {
        IMAGE_NAME = 'kimheang68/react-jenkin'
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
        
        node('Built-In Node') {
                stage('Check for Existing Image') {
                steps {
                    script {
                        def imageExists = sh(script: "docker image ls -q ${env.IMAGE_NAME}", returnStatus: true)
                        if (imageExists == 0) {
                            sh "docker rmi ${env.IMAGE_NAME}"
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

        }
        
        stage ('Deploy') {
            steps {
                script {
                    sh 'docker run  -p 3000:80 -d ${env.IMAGE_NAME}'
                }
            }
        }
    }
}
