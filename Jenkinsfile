pipeline {
    agent {
        kubernetes {
            //label 'kube-agent'
            yaml '''
                apiVersion: v1
                kind: Pod
                metadata:
                  labels:
                    jenkins: agent
                spec:
                  containers:
                  - name: jnlp
                    image: jenkins/inbound-agent:latest
                    //image: jenkins/inbound-agent:3107.v665000b_51092-4 
                  - name: docker
                    image: docker:latest
                    command:
                    - cat
                    tty: true
                    volumeMounts:
                      - name: docker-sock
                        mountPath: /var/run/docker.sock
                  volumes:
                    - name: docker-sock
                      hostPath:
                        path: /var/run/docker.sock
            '''
        }
    }
    stages {

        stage('Checkout') {
          steps {
            script {
               // The below will clone your repo and will be checked out to master branch by default.
               git credentialsId: 'dockerhub-credentials1', url: 'https://github.com/yidgar11/rmqp-example.git'
               // Do a ls -lart to view all the files are cloned. It will be clonned. This is just for you to be sure about it.
               sh "ls -lart ./*" 
               // List all branches in your repo. 
               sh "git branch -a"
              }
           }
        }
        
        stage('Build') {
            steps {
                container('docker') {
                    sh 'ls -l' 
                    sh 'docker --version'
                }
            }
        }
        
        stage('Test') {
            steps {
                container('docker') {
                    sh 'docker --version'
                }
            }
        }
    }
}
