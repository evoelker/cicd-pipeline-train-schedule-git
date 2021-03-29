pipeline {
    agent {
        kubernetes {
            cloud 'MasterPi ARM Cluster'
            defaultContainer 'jnlp'
            inheritFrom 'kube-worker-dind'
            namespace 'ci-cd'
        }
    }
    environment {
        DOCKER_IMAGE_NAME = "eryk81/train-schedule"
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
                        app = docker.build("${DOCKER_IMAGE_NAME}")
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
                            app.push("${BUILD_NUMBER}")
                            app.push("latest")
                        }
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
                kubernetesDeploy(
                    kubeconfigId: 'df57ea49-c6e3-4652-ba8a-d45c26b7fc85',
                    configs: 'deployment-train-schedule.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}