pipeline {
    agent any

    environment {
        REGISTRY = "http://localhost:5000"
        IMAGE = "node-app-image:${env.BUILD_ID}"
    }

    stages {

        stage('Build') {
            steps {
               timeout(time: 60, unit: "SECONDS") {
                   script {
                       nodeImage = docker.build("${IMAGE}", "-f Dockerfile .")
                    }
                }
            }
        }

        stage('Unit Tests') {
            steps {
               timeout(time: 60, unit: "SECONDS") {
                   script {
                           nodeImage.withRun('-p 3000:3000') { c ->
                                    sh "docker exec ${c.id} npm run test:unit"
                      }
                   }
                }
            }
        }

        stage('Integration Tests') {
            steps {
               timeout(time: 120, unit: "SECONDS") {
                   script {
                           nodeImage.withRun('-p 3000:3000') { c ->
                                sh "docker exec ${c.id} npm run test:integration"

                      }
                   }
               }
            }
        }

        stage('Staging') {
            steps {
               timeout(time: 120, unit: "SECONDS") {
                   script {
                            docker.withRegistry("${env.REGISTRY}") {
                                nodeImage.push("${env.BUILD_ID}")
                         }
                      }
                   }
               }
            }
        }
    }
