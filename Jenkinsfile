pipeline {
    agent any
    //agent { label 'my-agent' }
    tools {
        maven 'Maven3'
    }
    
    stages {
        
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

        stage ("Quality gate") {
            steps {
                    timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        stage ("Binary upload") {
            steps {
                    //nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: '019d12fb-b008-440e-8509-fcd92630e7fc', groupId: 'com.dept.app', nexusUrl: 'ec2-54-242-207-203.compute-1.amazonaws.com:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
                }
            }
            
        stage ("DEV deploy") {
            steps {
                deploy adapters: [tomcat9(credentialsId: '7e8c86ff-abae-4b19-b6ad-8b4fe4ccd761', path: '', url: 'http://ec2-54-166-218-97.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        
        stage ("DEV notify") {
            steps {
                slackSend channel: 'dec-2024-weekday-batch', message: 'Hi DEV team, your app has been deployed into DEV successfully. please start basic testing..'
            }
        }

        //CD starts here..
        stage ("DEV approval") {
            steps {
            echo "Taking approval from DEV Manager for QA Deployment"     
            timeout(time: 2, unit: 'DAYS') {
            input message: 'Do you approve QA Deployment?', submitter: 'admin,dev_mgr@gmail.com'
            }
        }
     }
     
    stage ("QA deploy") {
        steps {
                deploy adapters: [tomcat9(credentialsId: '7e8c86ff-abae-4b19-b6ad-8b4fe4ccd761', path: '', url: 'http://ec2-54-166-218-97.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
        }
    }
    
    stage ("QA notify") {
        steps {
            slackSend channel: 'dec-2024-weekday-batch,qa-testing-team', message: 'Hi QA team,  application has been deployed into QA env successfully. please start your functional testing..'
        }
    }
    stage ("QA approval") {
        steps {
            echo "Taking approval from QA Manager for PROD Deployment"     
            timeout(time: 2, unit: 'DAYS') {
            input message: 'Do you approve PROD Deployment?', submitter: 'admin,dev_mgr@gmail.com'
            }
        }
    }
    
    stage ("PROD deploy") {
        steps {
                deploy adapters: [tomcat9(credentialsId: '7e8c86ff-abae-4b19-b6ad-8b4fe4ccd761', path: '', url: 'http://ec2-54-166-218-97.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
        }
    }
    
    stage ("final notify") {
        steps {
            slackSend channel: 'dec-2024-weekday-batch,qa-testing-team,product-owners-teams', message: 'Hi  team,  application has been deployed into prod env successfully. please inform end customers..'
        }
      }
    }


}
