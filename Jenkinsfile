pipeline {
    agent any
    tools {
        nodejs 'nodejs'
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

            }
        }
stage('Build Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-cred'
                , passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    script {
                        sh 'docker build -t kimheang68/react-jenkin .'
                        sh "echo \$PASS | docker login -u \$USER --password-stdin"
                        sh 'docker push kimheang68/react-jenkin'
                    }
                }
            }
        }
stage ('Deploy') {
            steps {
                script {
                    sh 'docker run  -p 3000:80 -d kimheang68/react-jenkin:latest'
                }
            }
        }
    }
}
