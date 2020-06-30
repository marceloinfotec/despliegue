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
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    script {
                        sh "docker pull gilardoni72/pruba:${env.BUILD_NUMBER}\"
                        try {
                            sh "docker stop prueba"
                            sh "docker rm prueba"
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "docker run --restart always --name prueba -p 80:80 -d gilardoni72/prueba:${env.BUILD_NUMBER}"
                    }
                }
            }
        }
    
    }
}
    
    
    
     
   
