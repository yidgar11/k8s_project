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
