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
        stage('archive artifacts') {
            steps {
                archiveArtifacts 'build.zip'  
            }
        }
        stage('copying artifacts to remotes') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Webserver', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'exec command', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*.html')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }        
        stage('moving artifacts into the right folder') {
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