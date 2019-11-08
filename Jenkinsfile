pipeline {
    agent any
    
    stages {
        stage('build') {
            steps {
                sh '''mkdir -p build
                cp index.html build/index.html
                echo ${BUILD_NUMBER} > relese.txt
                cp release.txt build/release.txt'''
            }
        }
        stage('create artifacts') {
            steps {
                sh 'zip -r build.zip build'
            }
        }
        stage('docker build/push') {     
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                        def app = docker.build("arturnac/docker-nodejs-demo:${BUILD_NUMBER}", '.').push()
                    }   
                }
            }                 
        }
        stage('archive artifacts') {
            steps {
                archiveArtifacts 'build.zip'  
            }
        }
        stage('copying artifacts to remotes') {
            steps {

            }
        }        
        stage('archive artifacts') {
            steps {
                script {
                    sh """ssh training@192.168.56.103 <<< EOF
                    cp /home/training/build/* /var/www/my_page
                    rm -rf /home/training/build
                    rm -rf /home/training/build.zip
                    exit
                    EOF"""
                }
            }
        }       
    }
}