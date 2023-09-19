pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }

    environment {
        DOCKER_REGISTRY = 'kimheang68'
        IMAGE_NAME = 'react-jenkin'
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
                    def containerId = sh(script: "docker ps -a --filter name=${env.CONTAINER_NAME} -q", returnStdout: true).trim()
                    sh "echo containerId is ${containerId}" 
                    if (containerId) {
                        sh "docker stop ${containerId}"
                        sh "docker rm ${containerId}"
                    } else {
                        sh "echo No existing container to remove"
                    }
                }
            }
        }
        stage('Build Image') {
            steps {
                script {
                    def buildNumber = currentBuild.number
                    def imageTag = "${IMAGE_NAME}:${buildNumber}"
                    sh "docker build -t ${DOCKER_REGISTRY}/${imageTag} ."

                    withCredentials([usernamePassword(credentialsId: 'docker-hub-cred',
                            passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "echo \$PASS | docker login -u \$USER --password-stdin"
                        sh "docker push ${DOCKER_REGISTRY}/${imageTag}"
                    }
                }
            }
        }
        // stage ('Deploy') {
        //     steps {
        //         script {
        //             sh "docker run -p 3000:80 -d --name ${env.CONTAINER_NAME} ${DOCKER_REGISTRY}/${imageTag}"
        //         }
        //     }
        // }
        stage('Clone Repository') {
            steps {
                script {
                    sh 'rm -rf argocd-app-config'
                    sh 'git clone https://github.com/KimheangKen/argocd-app-config.git'
                    
                }
            }
        }
        stage('Update Deployment File') {
            steps {
                script {
                    def imageVersion = 'new-image-version' // Get the new image version
                    sh "sed -i 's/{{IMAGE_VERSION}}/${imageVersion}/' argocd-app-config/dev/myapp-deployment.yaml"
                }
            }
        }


        stage('Commit and Push Changes') {
            steps {
                script {
                    sh 'git config --global user.email "kimheangken68@gmail.com"'
                    sh 'git config --global user.name "KimheangKen"'
                    sh 'git add argocd-app-config/dev/myapp-deployment.yaml'
                    sh 'git commit -m "Update image version"'
                    sh 'git push origin main' // Push to the main branch, adjust if needed
                }
            }
        }

        // stage('Clean Up') {
        //     steps {
        //         script {
        //             sh 'cd ..' // Move out of the repository directory
        //             sh 'rm -rf argocd-app-config' // Remove the cloned directory
        //         }
        //     }
        // }

    }
}
