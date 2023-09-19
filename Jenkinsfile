pipeline {
  agent any
  stages {
    stage('Update dockerfile') {
      steps {
      script {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withCredentials([usernamePassword(credentialsId: 'git-hub-cred', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')])  {
                        
                        sh "git config user.email kimheangken68@gmail.com"
                        sh "git config user.name KimheangKen"
                        sh "sed -i 's+kimheang68/react-jenkin.*+kimheang68/react-jenkin:${DOCKERTAG}+g' dev/myapp-deployment.yaml"
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/argocd-app-config.git HEAD:main"
          
        }
      }
      }
  }
}
}
}