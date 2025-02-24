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
                  - name: helm
                    image: lachlanevenson/k8s-helm:v3.1.1
                    command:
                    - cat
                    tty: true
                    privileged: true
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
        options {
            ansiColor('xterm')
        }
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials1') 
    }
    
    stages {
        stage('Checkout') {
          steps {
            script {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials1', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo "Cloning repository using credentials..."
                        git clone https://$USERNAME:$PASSWORD@github.com/yidgar11/rmqp-example.git
                    '''
                }
                
                echo "\033[1;32m[Success] \033[0m Repository cloned successfully! " 

                echo "jenkins project files: "
                sh "ls -l " 
                
                echo "rmqp-example project files: "
                sh "ls -l rmqp-example/*"
                
                sh "git branch -a"
              }
           }
        }
        
        stage('Build') {
            steps {
                container('docker') {
                    sh 'docker build -t yidgar11/consumer:1.0 -f rmqp-example/consumer/Dockerfile rmqp-example/consumer'
                    sh 'docker build -t yidgar11/producer:1.0 -f rmqp-example/producer/Dockerfile rmqp-example/producer'
                    echo "\033[1;32m[Success] \033[0m Docker imags built successfully! " 

                    //echo "\033[1;33m[Info]    \033[0m $1"
                    //echo "\033[1;31m[Error]   \033[0m $1"
                }
            }
        }

        stage('Push') {
            steps {
                container('docker') {
                    script {
                         withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials1', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                         sh '''
                            echo "DockerHub Login using credentials..."
                            docker login docker.io -u $USERNAME -p $PASSWORD
                         '''
                         echo "\033[1;32m[Success] \033[0m DockerHub Login successfully! " 
                        }
                        sh 'docker push yidgar11/consumer:1.0'
                        sh 'docker push yidgar11/producer:1.0'
                        echo "\033[1;32m[Success] \033[0m images pushed to DockerHub successfully! " 
                    }
                }
            }
        }      

        
        stage('Test') {
            steps {
                container('docker') {
                    sh 'docker images | grep -e "producer" -e "consumer" '
                    echo "\033[1;32m[Success] \033[0m test successfully! " 
                }
            }
        }

        stage('helm') {
            steps {
                container('helm') {
                    sh 'cd  k8s-project/ ; helm template k8s-project .'
                    echo "\033[1;32m[Success] \033[0m helm successfully! " 
                }
            }
        }
    }
}
