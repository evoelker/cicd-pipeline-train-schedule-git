pipeline {
    agent {
        kubernetes {
            cloud 'MasterPi ARM Cluster'
            defaultContainer 'jnlp'
            inheritFrom 'kube-worker-dind'
            namespace 'ci-cd'
        }
        // kubernetes {
        //     // Empty Commit
        //     label 'kube-worker-dind'
        //     // label 'kube-worker'
        //     defaultContainer 'jnlp'
        // }
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation.'
                withGradle {
                    sh '''#!/bin/bash
                        chmod +x gradlew
                        ./gradlew build --no-daemon
                    '''
                }
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                container('dind-build') {
                    script {
                        app = docker.build('eryk81/train-schedule')
                        app.inside {
                            sh 'echo $(curl localhost:8080)'
                        }
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                container('dind-build') {
                    script {
                        docker.withRegistry('https://registry.hub.docker.com', '323e0e0d-ab6e-4a64-ac62-c10634c48c47') {
                            app.push("${env.BUILD_NUMBER}")
                            app.push("latest")
                        }
                    }
                }
            }
        }
    }
}