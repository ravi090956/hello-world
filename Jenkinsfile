pipeline {
    
    agent any
    
    tools {
        maven "3.9.8"
    }
    
    triggers {
        pollSCM '* * * * *'
    }
    stages {

        stage("test") {

            steps {
                powershell 'Get-Location'
            }
        }
        
        stage("checkout") {
            
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ravi090956/hello-world.git']])
            }
        }

        stage ('copy') {

            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'docker', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//docker', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '/*.xml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
            
        }
        
        stage ('build') {
            
            steps {
                
                powershell 'mvn clean install package'
                
            }
        }
        stage ('archive artifacts') {
            
            steps {
                
                archiveArtifacts artifacts: 'webapp/target/*.war', followSymlinks: false
                
            }
        }
        
        stage ('Creating container images') {
            
            steps {
                
                sshPublisher(publishers: [sshPublisherDesc(configName: 'docker', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'docker stop testing ; docker rm -f testing ; docker image rm -f image ; cd /opt/docker; docker build -t image .', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//docker', remoteDirectorySDF: false, removePrefix: 'webapp/target', sourceFiles: 'webapp/target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                
            }
        }
        
        stage ('Creating container') {
            
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'docker', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'docker run -td -p 8090:8080 --name testing image /bin/bash', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
        
        stage ('starting tomcat') {
            
            steps {
                
                sshPublisher(publishers: [sshPublisherDesc(configName: 'docker', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'docker exec testing /bin/sh -c "cd bin; sh startup.sh"', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}
