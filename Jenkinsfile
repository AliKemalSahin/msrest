pipeline 
{
    agent any
    environment {
        DOCKER_IMAGE_NAME = "alikemal/deployjava"
    }
    tools 
    {
        maven 'M3'
    }
    stages 
    {
        stage('Build Jar')
        {
            steps 
            {
                sh '''
                 mvn clean package
                 cd target
                 cp rest-service-0.0.1-SNAPSHOT.jar rest-service.jar 
                '''
                stash includes: 'target/*.jar', name: 'targetfiles'
            }
        }
        stage('Build Docker Image') 
        {
            steps 
            {
                script 
                {
                    app = docker.build(DOCKER_IMAGE_NAME) 
                }
            }
        }
        stage('Push Image') 
        {
            steps 
            {
                script 
                {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') 
                    {
                        app.push("latest")
                    }
                }
            }
        }
         stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                sh 'chmod 777 /var/run/docker.sock'
                sh 'kubectl apply -f kuber.yaml'
                
            }
        }
        
    }
    
}

