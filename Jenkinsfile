node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'github-auth-token', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh "git config user.email ettynan@gmail.com"
                    sh "git config user.name ettynan"
                    sh "cat vote-ui-deployment.yaml"
                    sh "sed -i 's+ettynan/vote.*+ettynan/vote:${DOCKERTAG}+g' vote-ui-deployment.yaml"
                    sh "cat vote-ui-deployment.yaml"
                    sh "git add ."
                    sh "git commit -m 'Done by Jenkins Jobs deployment: ${env.BUILD_NUMBER}'"
                    sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/vote-deploy.git HEAD:main"
                }
            }
        }
    }

    stage('Trigger deployment') {
       //: agent any
        environment {
            def GIT_COMMIT = "${env.GIT_COMMIT}"
        }
        steps {
            echo "${GIT_COMMIT}"
            echo "triggering deployment"
            // passing variables to job deployment run by vote-deploy repository Jenkinsfile
            build job: 'deployment', parameters: [string(name: 'DOCKERTAG', value: GIT_COMMIT)]
        }
    }
}
