pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }

    environment {
        IMAGE_NAME = 'kimheang68/react-jenkin'
        CONTAINER_NAME = 'my-container' // Specify the name of your container
        ARGOCD_SERVER = 'localhost:8888' // Set your Argo CD server URL
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
        // stage('Check for Existing Container') {
        //     steps {
        //         script {
        //             def containerId = sh(script: "docker ps -a --filter name=${env.CONTAINER_NAME} -q", returnStdout: true).trim()
        //             sh "echo containerId is ${containerId}" 
        //             if (containerId) {
        //                 sh "docker stop ${containerId}"
        //                 sh "docker rm ${containerId}"
        //             } else {
        //                 sh "echo No existing container to remove"
        //             }
        //         }
        //     }
        // }
        
        // stage('Build Image') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'docker-hub-cred',
        //                 passwordVariable: 'PASS', usernameVariable: 'USER')]) {
        //             script {
        //                 sh "docker build -t ${env.IMAGE_NAME} ."
        //                 sh "echo \$PASS | docker login -u \$USER --password-stdin"
        //                 sh "docker push ${env.IMAGE_NAME}"
        //             }
        //         }
        //     }
        // }
        // stage ('Deploy') {
        //     steps {
        //         script {
        //             sh "docker run -p 3000:80 -d --name ${env.CONTAINER_NAME} ${env.IMAGE_NAME}"
        //         }
        //     }
        // }

        stage('Deploy to Argo CD') {
            steps {
                script {
                    // Generate a JWT token that expires in 7 days
                    def authToken = sh(script: 'openssl rand -base64 32 | docker run --rm -i atmoz/jwt -g \'{"exp": 168 }\' -S supersecret', returnStdout: true).trim()

                    // Set Argo CD application details
                    def repoURL = 'https://github.com/KimheangKen/argocd-app-config.git'
                    def path = 'dev'
                    def namespace = 'myapp'
                    def appName = 'your-app'

                    // Download and install Argo CD CLI
                    sh "curl -sSL -o /usr/local/bin/argocd https://${ARGOCD_SERVER}/download/argocd-linux-amd64"
                    sh "chmod +x /usr/local/bin/argocd"

                    // Log in to Argo CD server
                    sh "argocd login ${ARGOCD_SERVER} --insecure --grpc-web --auth-token ${authToken}"

                    // Create or sync the Argo CD application
                    sh "argocd app create ${appName} --repo ${repoURL} --path ${path} --dest-server https://kubernetes.default.svc --dest-namespace ${namespace} --sync-policy automated"
                    sh "argocd app sync ${appName}"
                }
            }
        }

    }
}
