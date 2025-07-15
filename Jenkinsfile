pipeline {
    agent any

    tools {
        maven 'Maven_3.5.2'
        nodejs 'Node_18'
        
    }

stages {
    stage('Setup Node.js 18') {
      steps {
        sh '''
          curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
          sudo apt-get install -y nodejs
          node -v
        '''
      }
    }


    

    stages {
        stage('Compile and Run Sonar Analysis') {
            steps {
                sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=jenkinsdevsecops -Dsonar.organization=jenkinsdevsecops -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=2f46609dbdc08ab6eaa038be3a898e8015803b47'
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

