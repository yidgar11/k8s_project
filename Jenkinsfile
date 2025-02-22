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
                echo 'Repository cloned successfully!'

                echo "jenkins project files: "
                sh "ls -l " 
                
                echo "rmqp-example project files: "
                sh "ls -l rmqp-example/*"
                
                sh "git branch -a"
              }
           }
        }
        
        stage('Build Consumer') {
            steps {
                container('docker') {
                    sh 'cd rmqp-example/consumer ; ls -l' 
                    sh 'docker build -t yidgar11/consumer:1.0 .'
                    sh 'cd ../../'
                }
            }
        }

        stage('Build Producer') {
            steps {
                container('docker') {
                    sh 'cd rmqp-example/consumer ; ls -l' 
                    sh 'docker build -t yidgar11/producer:1.0 .'
                    sh 'cd ../../'
                }
            }
        }      
        
        stage('Test') {
            steps {
                container('docker') {
                    sh 'docker imnages'
                }
            }
        }

        // stage('Push to Docker Hub') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
        //             sh '''
        //                 echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
        //                 docker push your-dockerhub-username/your-repo:$BUILD_NUMBER
        //                 docker logout
        //             '''
        //         }
        //         echo 'Docker image pushed to Docker Hub!'
        //     }
        // }
    }
}
