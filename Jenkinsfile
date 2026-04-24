pipeline {
    agent any

    environment {
        // Ensure Jenkins looks in /usr/local/bin where we put the link
        PATH = "/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    }

    stages {
        stage('Deploy to Kubernetes') {
            steps {
                echo "Applying Kubernetes Manifests..."
                // This will now work!
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl rollout restart deployment/java-docker-app'
            }
        }
        
        stage('Verify Pods') {
            steps {
                sh 'kubectl get pods'
            }
        }
    }
}
