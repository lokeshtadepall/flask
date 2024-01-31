pipeline {
    agent any
        environment {
                PROJECT_ID = 'office-411914'
                CLUSTER_NAME = 'cluster-1'
                LOCATION = 'us-central1'
                CREDENTIALS_ID = 'kubernetes'
        }

    stages {
            stage('Build Docker Image') {
                    steps {
                            script {
                                    sh "docker build -t lokeshtadepalli267/dev:${env.BUILD_ID} ."
                            }
                    }
            }
        
            stage("Push Docker Image") {
                    steps {
                            script {
                                    echo "Push Docker Image"
                                    withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                                        sh "docker login -u lokeshtadepalli267 -p ${dockerhub}"
                                        sh "docker push lokeshtadepalli267/dev:${env.BUILD_ID}"
                                    }
                            }
                    }
            }

            stage('Deploy to K8s') {
                    steps{
                                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml" 
                                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
                            echo "Deployment Finished ..."
                    }
             }
       }
}
