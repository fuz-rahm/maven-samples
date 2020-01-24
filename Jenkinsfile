pipeline {
    agent {
        label 'master'
    }
    tools {
        maven 'maven'
        jdk 'java8'
        nodejs "Node10"
        
    }  

    stages {
        stage('clean workspace') {
            steps{
                cleanWs()
            }
        }

        stage('Checkout from Github') {
        
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/mr/addpa11y-ci']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/fuz-rahm/maven-samples.git']]])
            }
        }


       
        stage('508 Testing'){
            steps {
                script {
                    sh 'rm -rf pa11y'

                    sh 'mkdir pa11y'
                    sh label: '', returnStdout: true, script: 'sh runpa11y.sh'
                     publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '', reportFiles: 'pa11y/index.html', reportName: '508 Testing Report', reportTitles: '508 Accessibility Report'])
                }
            }
         }

          stage('Performance Tests') {
            agent {
            label 'master'
            }
            when {
              branch 'master'
            }
             steps {
                  deleteDir()
                  checkout scm
                  sh 'npm install'
                    sh 'npm run lighthouse'
                }
        }

      

        stage('build') {
            steps {
                sh 'echo "path: ${PATH}"'
                sh 'echo "M2_HOME: ${M2_HOME}"'
                sh 'mvn clean install -Dmaven.test.failure.ignore=true'
            }
        }



        
    }

    post {
        always {
            publishHTML (target: [
            allowMissing: false,
            alwaysLinkToLastBuild: false,
            keepAll: true,
            reportDir: '.',
            reportFiles: 'lighthouse-report.html',
            reportName: "Lighthouse"
            ])
        }
    }

    

    //post {
        //always {
            //archive "target/**/*"
            //junit 'target/surefire-reports/*.xml'
        //}
        //success {
            //mail to:"mrahman@cynerge.com", subject:"SUCCESS: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", body: "Yay, we passed."
        //}
        //failure {
            //mail to:"mahfuzurrahm518@gmail.com", subject:"FAILURE: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", body: "Boo, we failed."
        //}
        //unstable {
            //mail to:"jenkinsemailnotification31@gmail.com", subject:"UNSTABLE: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", body: "Huh, we're unstable."
        //}
    //}

    


    
}