pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

    triggers {
         pollSCM('* * * * *')
     }
  
    stages {

//  Checkout Git Repository
        stage("Git Checkout - Production"){
            when { branch 'master' }
            steps {
                checkout scm
                }
        } 

        stage("Build Image  - Production "){
            when { branch 'master' }
            steps{
                dir("maven-sample"){
                    sh 'mvn package'
                }
                
            }
        } 


//  Deploy war file to Webserver

        stage("Deploy Image - Production "){
            when { branch 'master' }
            options {
                timeout(time: 10, unit: 'MINUTES')
            }

            
             steps{
                input message: "Press Proceed to continue deployment", submitter:"jenkinsadmin,pinmicroadmin"
                sh '''#!/usr/bin/env bash
                    rsync -av --rsync-path="sudo rsync" maven-sample/target/*.war ubuntu@10.0.28.8:/var/lib/tomcat9/webapps/
                    ssh -o StrictHostKeyChecking=no ubuntu@10.0.28.8 << ENDSSH
                    sudo systemctl restart tomcat9
                    ENDSSH
                '''
                           
                
            } 
        } 

    }  
            


    post {

        always {
           // Wipe out the workspace before we finish!
            deleteDir()
            cleanWs()
        

        }
        success {
            // Wipe out the workspace before we finish!
            deleteDir()
            cleanWs()
            // Send Success Message to Team
        }
        failure {
            echo "Build failed"
            deleteDir()
            cleanWs()
            // Send Failure Message to Team                
        }
    }

}        

