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
                checkout([$class: 'GitSCM', branches: [[name: '*/mr/addpa11y-ci']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/fuz-rahm/simple-java-maven-app.git']]])
            }
        }

        stage ('Unit Test') {
            steps{
                sh 'mvn clean verify'
            }
        }
        stage ('Lighthouse'){
            steps{
                sh 'lighthouse-batch -s https://cynerge.com'
                sh 'ls report/lighthouse'
                lighthouseReport './report/lighthouse/cynerge_com.report.json'
            }
        }

        stage('build') {
            steps {
                sh 'echo "path: ${PATH}"'
                sh 'echo "M2_HOME: ${M2_HOME}"'
                sh 'mvn clean install -Dmaven.test.failure.ignore=true'
            }
        }

        //  stage('Pa11y') {
        //     steps {
        //          sh 'ls -a'
        //         sh 'pa11y-ci --config .pa11yci.json'
        //     }

        // }

        // stage('SonarQube analysis') {
        //     tools {   
        //         sonarQube 'SonarQube Scanner 4.8'
        //     }
        //     steps {
        //         withSonarQubeEnv('SonarQube Scanner') {
        //         sh 'sonar-scanner'
        //         }
        //     }
        // }
    }

    post {
        always {
            archive "target/**/*"
            junit 'target/surefire-reports/*.xml'
        }
        success {
            mail to:"mrahman@cynerge.com", subject:"SUCCESS: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", body: "Yay, we passed."
        }
        failure {
            mail to:"mahfuzurrahm518@gmail.com", subject:"FAILURE: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", body: "Boo, we failed."
        }
        unstable {
            mail to:"jenkinsemailnotification31@gmail.com", subject:"UNSTABLE: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", body: "Huh, we're unstable."
        }
    }     
    
}