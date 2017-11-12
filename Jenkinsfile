#!groovy

/* 
Declarative Pipeline Format Required! 
Potential Jenkins / Nexus Integration Design
Prereq plugins:
Pipeline Utility Steps
Nexus Artifact Uploader
*/

pipeline {
    environment{
        CI = 'true'
        //NM_GROUP_NAME = 'realestate'
        NM_APP_NAME = 'PlayingWithVue'
        //Version Info
        //update agent
        NM_APP_VER = "1.0.0"
        NM_APP_MAJOR_VER = "1"
        NM_APP_MINOR_VER = "0.0"
        //Tool Variables
        NODE_JS_VER = 'NodeJS 8.2.1'  
        //Azure Vars for KUDU deploys.  Jenkins plugin vars here, but plugin not currently working.
        //DEPENDS ON HOSTS ENTRIES ON BUILD SERVER UNTIL DNS WORK IS COMPLETED
        // NM_AZURE_RESOURCE_GROUP = 'nmbankdb-lab'
        // NM_AZURE_APP_NAME = 'nmbankdb-spa'
        // NM_AZURE_CRED_ID = 'Azure-Hollyvb-Deploy-Credentials'
        // NM_AZURE_KUDU_DEPLOY_URL = "https://${NM_AZURE_APP_NAME}.scm.ase1lab.com/api/zipdeploy"
        // NM_AZURE_KUDU_CREDENTIALS = credentials("${NM_AZURE_CRED_ID}")
    }
    triggers { 
        // listens for PUSH from Github
        githubPush()
    }
    // agent definitions can be at the pipeline (as here)
    agent any
    stages{

        stage ('Initial Git'){
            // when { branch 'azure-deploy'}
            steps {
                //obtains SHORT_GIT_COMMIT for use throughout the pipeline and changes the Jenkins build number display
                echo 'Obtaining SHORT_GIT_COMMIT'
                echo '%SONAR_HOST_URL'
                sh 'git rev-parse HEAD > commit'
                // sh 'git log -1 --pretty=format:" %%ae git hash %%h  git commit msg: %%s%%n" > slackmsg'
                sh 'git log -1 --pretty > slackmsg'
                //script blocks can be used to execute traditional Scripted Pipeline code when equivalent Declarative syntax isn't yet available
                script {
                    slack_msg = readFile('slackmsg').trim()
                    SLACK_MESSAGE = slack_msg
                }
                script {
                    git_commit = readFile('commit').trim()
                    SHORT_GIT_COMMIT = git_commit.take(7)
                    currentBuild.displayName = SHORT_GIT_COMMIT
                }
            }
        }


        // stage ('Build NodeJS from Source'){
        //     when { branch 'azure-deploy'}
        //     steps {
        //         echo "Executing NodeJS Build"
        //         bat "npm --version"
        //         bat "npm install"
        //         bat "npm run build"
        //     }               
        // }


        // stage ('Test NodeJS'){
        //     when { branch 'azure-deploy'}
        //     steps {
        //         echo "Executing NodeJS Build"
        //         //remove exit 0
        //         bat "npm run ci-test || exit 0"
        //         dir(path: './build'){
        //             echo "Zipping Build Artifacts"
        //                 script{
        //                     zip zipFile: '.\\deploy.zip', dir: '.\\'
        //                 }
        //         }                   
        //     }
        // }
        

        
        // stage ('Azure Deploy to Lab'){
        //     when { branch 'azure-deploy'}
        //     steps {
        //         echo "Azure Deploy via Curl / Kudu"
        //         //powershell "Invoke-RestMethod -Infile ./deploy.zip -Method Post -Uri ${NM_AZURE_KUDU_DEPLOY_URL}"
        //         bat "curl.exe --insecure -v -u ${NM_AZURE_KUDU_CREDENTIALS_USR}:${NM_AZURE_KUDU_CREDENTIALS_PSW} --upload-file .\\build\\deploy.zip -X POST ${NM_AZURE_KUDU_DEPLOY_URL}"
        //     }
        // }        
    }

    post {
        /* These steps will not appear as a Blue Ocean pipeline step, but will be executed and shown in the log */
        always {
            // deleteDir() will clean the workspace, ensuring fewer errors in future builds.  Remove if other pipelines will be referencing this workspace.
            echo 'cleansing workspace'
            deleteDir()
            // Slack Integration, uses slack plugin
            script{
                // def msg = "Test Message - Build and Deploy Complete for ${NM_APP_NAME}, CommitHash: ${SHORT_GIT_COMMIT}"
                // def msg = "Test Message - Build Complete for ${NM_APP_NAME}.  JSON deployment results at https://${NM_APP_NAME}.scm.ase1lab.com/api/deployments"
                def msg = SLACK_MESSAGE
                def color = '#D4DADF'
                slackSend(color: color, message: "Build Complete for ${NM_APP_NAME}."+msg, channel: "#hackatron3000")
            }
        }
        success {
            echo 'Success'
        }
        failure {
            echo 'Failure'
        }
    }
}
