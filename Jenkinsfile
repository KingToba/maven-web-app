pipeline {
    agent any
    tools {
        maven "jenkins-maven"
    }
    stages {
        stage('Git clone') {
            steps {
                script {
                    git 'https://github.com/Kenmakhanu/maven-web-app.git';
                }
            }
        }
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
                script{
                 def mavenPom = readMavenPom file:'pom.xml'
                 def nexusRepoName = mavenPom.version.endsWith("SNAPSHOT") ? "Kens-repo-snapshot" : "kens-repo-release"
                 nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'maven-web-application', 
                        classifier: '', 
                        file: "target/maven-web-application-${mavenPom.version}.war",
                        type: 'war'
                    ]
                ],
                credentialsId: 'nexus-user-credentials',
                groupId: 'com.mt',
                nexusUrl: '50.18.33.101:8081',
                nexusVersion: 'nexus3',
                protocol: 'http', 
                repository: nexusRepoName,
                version: "${mavenPom.version}"
               }
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
        //stage('Archieve artifacts in Nexus'){
           // steps{
              //  timeout(time:5, unit:'DAYS'){
                //    input message:'Approve artifact archiving?'
             //   }
              //  build job: 'Jenkins_Nexus'
          //  }
       // }
    }
}
