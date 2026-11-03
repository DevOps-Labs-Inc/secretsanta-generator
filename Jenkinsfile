pipeline {
    agent { label 'slave-1' }

    tools {
        maven 'maven3'
        jdk 'jdk21'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test Cases') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Maven Verification') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-scanner') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=santa \
                        -Dsonar.projectKey=santa \
                        -Dsonar.java.binaries=.'''
                }
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn package'
            }
        }
        
      stage('Build Docker Image') {
            steps {
                script{
                    // This step should not normally be used in your script. Consult the inline help for details.
withDockerRegistry(credentialsId: 'docker-cred') {
                    sh "docker build -t santa:latest ."
                    }
                }
            }
        }
        
        stage('Build Tag & Push') {
            steps {
                script{
                    // This step should not normally be used in your script. Consult the inline help for details.
withDockerRegistry(credentialsId: 'docker-cred') {
                    sh "docker tag santa:latest mayurjagtap15/santa:latest1"
                    sh "docker push mayurjagtap15/santa:latest1"
                    
                    }
                }
            }
        }
        
        stage('Docker Deploy Application') {
            steps {
                script{
                    // This step should not normally be used in your script. Consult the inline help for details.
withDockerRegistry(credentialsId: 'docker-cred') {
                    sh "docker run -d -p 8081:8080 mayurjagtap15/santa:latest1 "
                    }
                }
            }
        }
    }

    post{
        always {
            emailext(
                subject: "Pipeline Status: ${BUILD_NUMBER}",
                body: '''$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:

Check console output at $BUILD_URL to view the results.''',
                to: 'mayurjagtap15@gmail.com',
                from: 'mayurjagtap15@gmail.com',
                replyTo: 'jenkins@example.com'
                )
        }
    }
}

