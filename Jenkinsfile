node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Update GIT') {
            script {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        //def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                        sh "git config user.email avindusridharmawardhana@gmail.com" //Own Github configured email
                        sh "git config user.name GitAvi001" //Own Github username
                        //sh "git switch master"
                        sh "cat deployment.yaml"
                        //sed command to change docker image name
                        sh "sed -i 's+avindudocker1/gitops-jenkins-build.*+avindudocker1/gitops-jenkins-build:${DOCKERTAG}+g' deployment.yaml" 
                        sh "cat deployment.yaml"
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/CI-CD-Jenkins-ArgoCD-KubernetesManifests.git HEAD:main" //"<Kubernetes_manifests_repository URL> HEAD:main"
      }
    }
  }
}
}
