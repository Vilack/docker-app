pipeline {
    agent any

    environment {
        IMAGE_NAME = "vilack21/myapp"
    }

    stages {

        stage('Build') {
            steps {
                sh 'cd /var/lib/jenkins/app && docker build -t $IMAGE_NAME .'
            }
        }

        stage('Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Blue-Green Deploy') {
            steps {
                sh '''
                # Remove old green
                docker rm -f green || true

                # Start new version as GREEN
                docker run -d -p 8085:80 --name green vilack21/myapp

                # Stop BLUE (old version)
                docker rm -f blue || true

                # Rename GREEN → BLUE (switch traffic)
                docker rename green blue
                '''
            }
        }
    }
}
