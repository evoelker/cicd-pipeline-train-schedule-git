pipeline {
    agent {
        // Empty Commit
        // label 'kube-worker-dind'
        label 'kube-worker'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation.'
                withGradle {
                    sh './gradlew build'
                }
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build('eryk81/train-schedule')
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
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