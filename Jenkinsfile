@Library('luffy-devops') _

pipeline {
    agent { label 'jnlp-slave'}
    options {
		timeout(time: 20, unit: 'MINUTES')
		gitLabConnection('gitlab')
	}
    environment {
        IMAGE_REPO = "192.168.136.25:5000/demo/myblog"
        IMAGE_CREDENTIAL = "credential-registry"
        DINGTALK_CREDS = credentials('dingTalk')
        PROJECT = "myblog"
    }
    stages {
        stage('checkout') {
            steps {
                container('tools') {
                    checkout scm
                }
            }
        }
        stage('CI'){
            failFast true
            parallel {
                stage('Unit Test') {
                    steps {
                        echo "Unit Test Stage Skip..."
                    }
                }
                stage('Code Scan') {
                    steps {
                        container('tools') {
                            script {
                               devops.scan().start()
                            }
                        }
                    }
                }
            }
        }
        stage('docker-image') {
            steps {
                container('tools') {
                    script{
                        devops.docker(
                            "${IMAGE_REPO}",
                            "${GIT_COMMIT}",
                            IMAGE_CREDENTIAL                          
                        ).build().push()
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                container('tools') {
                    script{
                    	devops.deploy("deploy",true,"deploy/deployment.yaml").start()
                    }
                }
            }
        }
        stage('integration test') {
            when {
                expression { BRANCH_NAME ==~ /v.*/ }
            }
            steps {
                container('tools') {
                    script{
                    	devops.robotTest(PROJECT)
                    }
                }
            }
        }
    }
    post {
        success { 
            script{
                devops.notificationSuccess(PROJECT,"dingTalk")
            }
        }
        failure {
            script{
                devops.notificationFailure(PROJECT,"dingTalk")
            }
        }
    }
}