def deployment_name = 'intdb-deployment'
pipeline {

    options {
        timeout(time: 30, unit: 'MINUTES')
    }
    agent { label 'master' }
    stages {
        stage('Checkout') {
            steps {
                script {
                    dir('Deployment') {
                        deleteDir()
                        checkout([$class: 'GitSCM', branches: [[name: 'master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git-cred', url: "https://github.com/lidorabo/INT_DB_CD.git"]]])

                    }


                }
            }
        }
        stage('Deployment with Kubernetes'){
            steps{
                script{
                    dir('Deployment'){
                        sh """
                               export PATH=/bin/bash:$PATH
                               export KUBECONFIG=/var/jenkins_home/admin.conf
                               kubectl apply -f db.yaml
                                kubectl patch deployment $deployment_name -p '{"spec":{"progressDeadlineSeconds":10}}'
                                if ! kubectl rollout status deployment $deployment_name;
                                    then
                                        kubectl rollout undo deployment $deployment_name
                                fi
                            """
                    }
                }
            }
        }
    }

}