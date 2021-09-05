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
                def mavenPom = readMavenPom 'pom.xml'
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'maven-web-application', 
                        classifier: '', 
                        file: "target/Kensapps-${mavenPom.version}.war",
                        type: 'war'
                    ]
                ],
                credentialsId: 'nexus-user-credentials',
                groupId: 'com.mt',
                nexusUrl: '54.189.189.148:8081',
                nexusVersion: 'nexus3',
                protocol: 'http', 
                repository: 'maven-build-repo',
                version: "${mavenPom.version}"
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
