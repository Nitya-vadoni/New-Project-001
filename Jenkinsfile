pipeline{
    agent any
    environment{
        DOCKER_USER ="nityavadoni"
        NODE_IMAGE = "${DOCKER_USER}/Node-app"
        NGINX_IMAGE = "${DOCKER_USER}/Nginx-app"
        VM_IP = "3.237.40.196"
    }

    stages{
        steps("Create docker image"){
            sh 'docker build -f Dockerfile-Node -t $NODE_IMAGE .'
            sh 'docker build -f Dockerfile-nginx -t $NGINX_IMAGE .'
        }
    

        stage("Login to Dockerhub"){
            steps {
                 withCredentials([usernamePassword(credentialsId: 'dockerhub_cred',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage("Push the images to dockerhub"){
            steps{
                sh 'docker push $NODE_IMAGE'
                sh 'docker push $NGINX_IMAGE'
            }
        }
        stage('Deploy to Target-VM') {
	     steps {
		 sshagent(['ec2-ssh-key']) {
    sh '''
    ssh -o StrictHostKeyChecking=no ubuntu@$VM_IP <<EOF

    echo "Running on: $(hostname)" 

    docker pull $Node_image 
    docker pull $Nginx_image

    docker network create node-app

    docker stop Nodejs || true
    docker rm Nodejs ||true

    docker stop Nginx || true
    docker rm Nginx || true

    docker run -d -p 3000:3000 --name Nodejs --network node-app $Node_IMAGE
    docker run -d -p 80:80 --name Nginx --network node-app $Nginx_IMAGE
    EOF
    '''
    }
    }
    }
    }
    }
