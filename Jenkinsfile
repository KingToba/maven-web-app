pipeline {
    agent any
    stages {
        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo "Now Archiving the Artifacts...."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('SonarQube Test') {
            steps {
                sh 'mvn sonar:sonar'
            }
        }
         stage('Nexus Archieve') {
            steps {
                //sh 'mvn deploy'
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'maven-web-application', 
                        classifier: '', 
                        file: 'target/Kensapps.war',
                        type: 'war'
                    ]
                ],
                credentialsId: 'nexus-user-credentials',
                groupId: 'com.mt',
                nexusUrl: 'http://54.189.189.148:8081/',
                nexusVersion: 'nexus2',
                protocol: 'http', 
                repository: 'maven-build-repo',
                version: '0.0.1'
            }
        }
        stage('Deploy to Tomcat_Staging'){
            steps{
                build job: 'Staging_Env'

            }
            
        }
        stage('Deploy to Tomcat_Prod'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve PRODUCTION Deployment?'
                }
                build job: 'Prod_Env'
            }
        }
        stage('Archieve artifacts in Nexus'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve artifact archiving?'
                }
                build job: 'Jenkins_Nexus'
            }
        }
    }
}
