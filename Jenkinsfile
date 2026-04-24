pipeline {
    agent any

    stages {
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Applying Kubernetes Deployment and Service..."
                    // This command pulls the image from DockerHub as defined in the YAML
                    sh 'kubectl apply -f deployment.yaml'
                    
                    echo "Triggering rollout to ensure latest image pull..."
                    sh 'kubectl rollout restart deployment/java-docker-app'
                }
            }
        }

        stage('Verify K8s Pod Status') {
            steps {
                script {
                    echo "Waiting for pods to reach 'Running' state..."
                    // Fulfills the requirement to verify pod is running successfully
                    sh 'kubectl rollout status deployment/java-docker-app --timeout=90s'
                    
                    echo "--- Current Pod List ---"
                    sh 'kubectl get pods -l app=java-app'
                    
                    echo "--- Service Details ---"
                    sh 'kubectl get svc java-app-service'
                }
            }
        }
    }

    post {
        success {
            echo "SUCCESS: Application deployed and verified on Kubernetes!"
        }
        failure {
            echo "FAILURE: Kubernetes deployment failed. Check logs."
        }
    }
}
