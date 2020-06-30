pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
               }
        }
        stage('Contruir Imagen Docker') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build("gilardoni72/prueba")
                    app.inside {
                        sh 'echo $(curl localhost:80)'
                    }
                }
            }
        }
        stage('Push a DockerHub') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
    }
}
    
    
    
     
   
