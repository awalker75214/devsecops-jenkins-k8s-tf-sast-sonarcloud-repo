pipeline {
    agent any

    tools {
        maven 'Maven_3.5.2'
        nodejs 'Node_18'
    }

    stages {
        stage('Check Node Version') {
            steps {
                withEnv(["PATH+NODE=${tool 'Node_18'}/bin"]) {
                    sh 'node -v'
                }
            }
        }

        stage('Compile and Run Sonar Analysis') {
            steps {
                withEnv(["PATH+NODE=${tool 'Node_18'}/bin"]) {
                    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=jenkinsdevsecops -Dsonar.organization=jenkinsdevsecops -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=b0214368e7e75db3056b89c7a9e5f9d3678263b0'
                }
            }
        }

        stage('Run SCA Analysis Using Snyk') {
            steps {
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh 'mvn snyk:test -fn'
                }
            }
        }

        stage('Build') {
            steps {
                withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                    script {
                        app = docker.build("asg")
                    }
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://121819057825.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                        app.push("latest")
                    }
                }
            }
        }
    }
}

