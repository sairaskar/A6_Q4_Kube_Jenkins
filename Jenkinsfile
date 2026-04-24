pipeline {
    agent any

    environment {
        // DockerHub Image Name
        IMAGE_NAME = "sairaskar/a6-q2-java-docker-app"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                echo "Building the Java Docker image..."
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Run & Verify Docker Locally') {
            steps {
                script {
                    echo "Running container to verify output..."
                    def output = sh(script: "docker run --rm $IMAGE_NAME", returnStdout: true).trim()
                    
                    echo "--- Container Output Start ---"
                    echo output
                    echo "--- Container Output End ---"

                    if (output.contains("Hello from Java inside Docker!")) {
                        echo "SUCCESS: Output verified!"
                    } else {
                        error "FAILURE: Unexpected output from container."
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'a6_q2_dockerhub_cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Applying Kubernetes Manifests..."
                // Applies the deployment and service defined in deployment.yaml
                sh 'kubectl apply -f deployment.yaml'
                
                // Forces the pods to pull the latest image you just pushed
                sh "kubectl rollout restart deployment/java-docker-app"
            }
        }

        stage('Verify K8s Deployment') {
            steps {
                script {
                    echo "Waiting for pods to be in Running state..."
                    // This command will block until the deployment is successful or times out
                    sh 'kubectl rollout status deployment/java-docker-app --timeout=60s'
                    
                    echo "Deployment Status:"
                    sh 'kubectl get pods -l app=java-app'
                    
                    echo "Service Details:"
                    sh 'kubectl get svc java-app-service'
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up local Docker images to save space..."
            sh "docker rmi ${IMAGE_NAME} || true"
        }
        success {
            echo "Pipeline completed successfully! Application is live on Kubernetes."
        }
        failure {
            echo "Pipeline failed. Check logs for errors."
        }
    }
}
