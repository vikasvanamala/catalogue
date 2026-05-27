pipeline {
    agent {
        node {
            label 'AGENT-1'
        }
    }
    environment {
        COURSE = "jenkins"
        appVersion = ""
        ACC_ID = "215446237872"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
    }
    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }
    stages {
        stage('Read Version') {
            steps {
                script {
                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "appversion: ${appVersion}"
                }
            }
        }
        stage('Installing Dependecies') {
            steps {
                 script {
                    sh """
                        npm install --include=dev
                    """    
                }  
            }
        }
        stage('Build Image') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: "us-east-1"){
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACCT_ID}.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t ${ACCT_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                            docker images
                            docker push ${ACCT_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                        """
                    }    
                }    
            }
        }
        stage('installing dependecies') {
            steps {
                 script {
                    sh """
                        npm install --include=dev
                    """    
                }  
            }
        }
  
    }
    post {
        always {
            echo 'i will always say hello/....'
            cleanWs()
        }
        success {
            echo 'I will run if success'
        }
        failure {
            echo 'I will run if failure'
        }
        aborted {
            echo 'pipeline is aborted'
        }
    }
}