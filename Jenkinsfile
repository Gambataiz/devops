 pipeline {
     agent any
     environment {
         DOCKER_CREDENTIALS = credentials('dockerhub-credentials')
	 KUBECONFIG = credentials('mykubeconfig')
     }
     stages {
         stage('Lint') {
             steps {
		  sh '''
      		  #!/bin/bash
                  python3 -m venv venv
                  . ./venv/bin/activate
                  pip install flake8
                  flake8 app.py
                  '''
	     }
         }
         stage('Format') {
             steps {
                 sh '''
                 #!/bin/bash
                 python3 -m venv venv
	         . ./venv/bin/activate
                 pip install black
                 black --check app.py
                 '''
             }
         }
         stage('Build') {
             steps {
                 sh 'echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIAL_USR --password-stdin'
                 sh 'docker build -t <dockerhub-username>/<repo-name>:0.0.1 .'
                 sh 'docker push <dockerhub-username>/<repo-name>:0.0.1'
             }
         }
         stage('Deploy to Kubernetes') {
             steps {
                 script {
                      sh 'curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/linux/amd64/kubectl'
                      sh 'chmod +x ./kubectl'
                      sh 'mv ./kubectl /usr/local/bin/kubectl'
                      sh 'export KUBECONFIG=$KUBECONFIG'
                      sh 'kubectl apply -f kubernetes/deployment.yaml'
                      sh 'kubectl rollout status deployment/flask-app'
            }
        }
    }
}

     }
 }
