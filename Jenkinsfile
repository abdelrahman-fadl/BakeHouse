pipeline {
    agent { label "slave1" }

    stages {
        stage('build') {
            steps {
                echo 'build'
                withCredentials([usernamePassword(credentialsId: 'iti-sys-admin-sohag-docker-cred', usernameVariable: 'USERNAME_SOHAG', passwordVariable: 'PASSWORD_SOHAG')]) {
                    sh '''
                        docker login -u ${USERNAME_SOHAG} -p ${PASSWORD_SOHAG}
                        docker build -t abdofadl/bakehouseitisohag:v${BUILD_NUMBER} .
                        docker push abdofadl/bakehouseitisohag:v${BUILD_NUMBER}
                    '''
                }
            }
        }
        stage('deploy') {
            
            steps {
                echo 'deploy'
                withCredentials([file(credentialsId: 'kubesecret', variable: 'KUBECONFIG_SOHAG')]) {
                    sh '''
                        mv Deployment/deploy.yaml Deployment/tmp.yaml
                        cat Deployment/tmp.yaml | envsubst > Deployment/deploy.yaml
                        rm -f Deployment/tmp.yaml
                        kubectl apply -f Deployment --kubeconfig ${KUBECONFIG_SOHAG}
                    '''
                }
            }
        }
    }
}
