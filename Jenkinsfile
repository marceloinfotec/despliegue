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
        stage('Deploy A Produccion') {
            when {
                branch 'master'
            }
            steps {
                input '¿ DESAPLIAGA HACIA PRODUCCION ?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    script {
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull gilardoni72/prueba:${env.BUILD_NUMBER}\""
                        try {
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop prueba\""
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm prueba\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run --restart always --name test -p 8081:80 -d gilardoni72/prueba:${env.BUILD_NUMBER}\""
                    }
                }
            }
        }
    }
}
    
    
    
     
   
