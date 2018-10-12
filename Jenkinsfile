pipeline {
    agent any
    stages {
        stage('Branch Close') {
            steps {
				mail body:"Any developer who submitted a recent change to the Main-Int branch, which has not been validated by a CI build check, please be available during the next couple of hours to help fix any potential build breaks." ,subject: "Main-Int is now CLOSED for '${env.BUILD_NUMBER}' Promotions",to: 'kkumarasamy@nextgen.com'
				sleep 15 //seconds
            }
        }
        stage('DBA'){
            steps {
                mail body: "Branch Closed for '${env.BUILD_NUMBER}' at ${env.BUILD_URL}",subject: "Build '${env.BUILD_NUMBER}'Required DBA Team Approval",to: 'karthiikumar@gmail.com'
                timeout(time:5,unit:'HOURS'){
                    input message : 'Approve DBA check-in',
                    submitter : 'dbuser'
                    sleep 15 //seconds
                }
            }
        }
        stage('Build'){
            steps {
                bat '''
                   "C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\MSBuild.exe" \"D:\\POPHealth\\NextGen PH-Administration v1.8.0\\NextGen PH-Administration v1.8.0.sln\" /p:Configuration=Release
                '''
                mail body:"Build '${env.BUILD_NUMBER}' Completed", subject: "Build '${env.BUILD_NUMBER} Completed",to: 'kkumarasamy@nextgen.com'
                sleep 15 //seconds
            }
		
        }
        stage('Deploy'){
            steps {
              parallel(
                "SmokeTestEnv": {      
                bat '''
                   msiexec /i "D:\\POPHealth\\NextGen PH-Administration v1.8.0\\NextGen PH - Administration v1.8.0xxxx.msi" /q
                    echo "Deployment Completed!"
                '''
                	mail body: "Deployment of Build '${env.BUILD_NUMBER}' Completed in Build Team Smoke test Env",subject: "Deployment of Build '${env.BUILD_NUMBER}' Completed-Build Team Smoke Test Env",to: 'kkumarasamy@nextgen.com'
                	sleep 15 //seconds
                },
                "QAManualTestEnv": { 
                  bat '''
                    echo "Deployment Completed!"
                '''
                mail body: "Deployment of Build '${env.BUILD_NUMBER}' in QA Manual Test env Completed",subject: "Deployment of Build '${env.BUILD_NUMBER}' Completed- QA Manual Test Env",to: 'kkumarasamy@nextgen.com'
                sleep 15 //seconds
                },
                "QAAutomatedTestEnv": { 
                  bat '''
                    echo "Deployment Completed!"
                '''
                mail body: "Deployment of Build '${env.BUILD_NUMBER}' in QA Automation Env Completed",subject: "Deployment of Build '${env.BUILD_NUMBER}' Completed-QA Automation Env",to: 'kkumarasamy@nextgen.com'
                sleep 15 //seconds
                }  
                )	
            }
		
        }    
        stage('Smoke Test'){
            steps {
            parallel(   
                "BuildTeamSmokeTest" : {    
                    bat '''
                        echo "Smoke Test Completed!"
                    '''
                    	mail body: "SmokeTest of Build '${env.BUILD_NUMBER}' Completed in Build env Completed", subject: "SmokeTest of Build '${env.BUILD_NUMBER}' Completed-Build Team Smoketest Env",to: 'kkumarasamy@nextgen.com'
                    	sleep 15 //seconds
                    },
                "QAAutomationTeamSmokeTest" : {    
                    bat '''
                        echo "Smoke Test Completed!"
                    '''
                    	mail body: "SmokeTest of Build '${env.BUILD_NUMBER}' in QA Automation Test Completed", subject: "SmokeTest of Build '${env.BUILD_NUMBER}' Completed-QA Automation Env",to: 'kkumarasamy@nextgen.com'
                    	sleep 15 //seconds  
                   }  
              ) 
            }    
        }
    }
}
