pipeline {
    agent any
    
    stages {
        stage('build') {
            steps {
                sh '''mkdir -p build
                cp my_page.html build/my_page.html
                echo ${BUILD_NUMBER} > release.txt
                cp release.txt build/release.txt'''
            }
        }
        stage('create artifacts') {
            steps {
                sh 'zip -r build.zip build'
            }
        }
        stage('archive artifacts') {
            steps {
                archiveArtifacts 'build.zip'  
            }
        }
        stage('copying artifacts to remotes') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Webserver', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'unzip build.zip', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*.zip')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])            }
        }        
        stage('moving artifacts into the right folder') {
            steps {
                script {
                    sh """ssh training@192.168.56.103 <<< EOF
                    cp /home/training/build/* /var/www/htmlß
                    rm -rf /home/training/build
                    rm -rf /home/training/build.zip
                    exit
                    EOF"""
                }
            }
        }       
    }
}