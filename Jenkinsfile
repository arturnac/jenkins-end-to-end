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
                shPublisher(publishers: [sshPublisherDesc(configName: 'academy', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'exec command', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*.html')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])        }
        stage('archive artifacts'){
            steps {
                script {
                    sh """ssh academy@10.0.2.4 <<< EOF
                    cp /home/academy/build/* /var/www/my_page
                    rm -rf /home/academy/build
                    rm -rf /home/academy/build.zip
                    exit
                    EOF"""
                }
            }
        }
    }

    post {
        failure {
            mail to: 'artur.na@hotmail.com',
            subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
            body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}