pipeline {
    agent any
    //agent { label 'my-agent' }
    tools {
        maven 'Maven3'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'f94e27a7-d1ff-4a8e-b9a3-616dc88fc9f2', url: 'https://github.com/akannan1087/myDec2024WeekdayBatch'
            }
        }
        
        stage ("Build") {
            steps {
                sh "mvn clean install -f MyWebApp/pom.xml"
            }
        } 
        
        
        stage ("Code coverage") {
            steps {
                jacoco()
            }
        }

        stage ("Code scan") {
            steps {
                withSonarQubeEnv("SonarQube") {
                sh "mvn sonar:sonar -f MyWebApp/pom.xml"
                }
            }
        }
        
        stage ("Binary upload") {
            steps {
                    nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: '019d12fb-b008-440e-8509-fcd92630e7fc', groupId: 'com.dept.app', nexusUrl: 'ec2-54-242-207-203.compute-1.amazonaws.com:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
                }
            }
            
        stage ("DEV deploy") {
            steps {
                deploy adapters: [tomcat9(credentialsId: '7e8c86ff-abae-4b19-b6ad-8b4fe4ccd761', path: '', url: 'http://ec2-54-175-41-38.compute-1.amazonaws.com:8090/')], contextPath: null, war: '**/*.war'
            }
        }
        
            
        stage ("DEV notify") {
            steps {
                slackSend channel: 'dec-2024-weekday-batch', message: 'Hi DEV team, your app has been deployed into DEV successfully. please start basic testing..'
            }
        }
    }
}
