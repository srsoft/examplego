pipeline {
    agent any
    tools {
        go 'go1.22.0'
    }
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0 
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    }
    stages {

        stage('Clone repository') {
            steps {
                checkout scm
            }
        }     
        
        stage("Build image") {
            steps {
                echo 'BUILD EXECUTION STARTED'
                echo "env.BUILD_NUMBER: ${env.BUILD_NUMBER}"
                sh 'go version'
                sh 'go get ./...'
                sh "docker build . -t harbor.ks.io:8443/example/go:${env.BUILD_NUMBER}"
            }
        }

        stage("Test image") {
            steps {
                echo 'UNIT TEST EXECUTION STARTED'
                echo "env.BUILD_NUMBER: ${env.BUILD_NUMBER}"
            }
        }        

        stage('Push image') {
            agent any
            steps {
                withCredentials([usernamePassword(credentialsId: 'harbor', passwordVariable: 'harborPassword', usernameVariable: 'harborUser')]) {
                    sh "docker login -u ${env.harborUser} -p ${env.harborPassword} https://harbor.ks.io:8443"
                    sh "docker push harbor.ks.io:8443/example/go:${env.BUILD_NUMBER}"
                }
            }
        }

        stage('Trigger ManifestUpdate') {
            steps {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
            }
        }        

    }



}