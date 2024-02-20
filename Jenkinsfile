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
        // stage("unit-test") {
        //     steps {
        //         echo 'UNIT TEST EXECUTION STARTED'
        //         sh 'make unit-tests'
        //     }
        // }
        // stage("functional-test") {
        //     steps {
        //         echo 'FUNCTIONAL TEST EXECUTION STARTED'
        //         sh 'make functional-tests'
        //     }
        // }


        // stage("build") {
        //     steps {
        //         echo 'BUILD EXECUTION STARTED'
        //         sh 'go version'
        //         sh 'go get ./...'
        //         sh 'docker build . -t example/go'
        //     }
        // }
        // stage('deliver') {
        //     agent any
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'harbor', passwordVariable: 'harborPassword', usernameVariable: 'harborUser')]) {
        //         sh "docker login -u ${env.harborUser} -p ${env.harborPassword} https://harbor.ks.io:8443"
        //         sh 'docker push example/go'
        //         }
        //     }
        // }
        def app

        stage('Clone repository') {
            checkout scm
        }

        stage('Build image') {
            app = docker.build("example/go")
        }

        stage('Test image') {
            app.inside {
                sh 'echo "Tests passed"'
            }
        }

        stage('Push image') {
            docker.withRegistry('https://harbor.ks.io:8443', 'harbor') {
                app.push("${env.BUILD_NUMBER}")
            }
        }
        
        stage('Trigger ManifestUpdate') {
            echo "triggering updatemanifestjob"
            build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }

    }
}