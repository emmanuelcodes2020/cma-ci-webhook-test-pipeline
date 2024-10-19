pipeline {
    agent {label 'node-1'}

    environment {
        NEXUS_REPOSITORY = "cma"
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "https"
        NEXUS_URL = "nexus.lamtech.io"
        NEXUS_CREDENTIAL_ID = "nexus-administrator"
        SONARQUBE_SERVER = 'https://sonarqube.lamtech.io'
    }

    stages {
        stage('Clone CMA Application') {
            steps {
                sh """
                git clone git clone --single-branch --branch cma-ci git@github.com:emmanuelcodes2020/cma-app-customer-management.git
                pwd
                """
            }
        }

        stage('Build Artifact using Maven') {
            steps {
                sh """
                cd cma-app-customer-management
                mvn clean package
                """
            }
        }

        stage('Unit Testing') {
            steps {
                sh """
                cd cma-app-customer-management
                mvn test
                """
            }
        }
        stage("build & SonarQube analysis") {
            steps {
                withSonarQubeEnv('SONARQUBE_SERVER') {
              sh 'cd cma-app-customer-management && mvn clean package sonar:sonar'
              }
            }
          }

        stage("Publish Artifact to Nexus Repository") {
            steps {
                sh 'cd cma-app-customer-management && ls -lrt'
                nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        groupId: 'com.business',
                        version: "cma-${env.BUILD_NUMBER}",
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        artifacts: [
                            [artifactId: 'customer-management',
                            classifier: '',
                            file: 'customer-management/target/customer-management-0.0.1-SNAPSHOT.jar ',
                            type: 'jar']
                        ]
                )
                
           }
        
        }
    }
        
    post {
        always {
            cleanWs()
        }
    }
}
