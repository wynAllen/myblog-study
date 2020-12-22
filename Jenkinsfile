pipeline {
   agent { label '192.168.136.27'}

   stages {
      stage('printenv') {
         steps {
            echo 'Hello World'
            sh 'printenv'
         }
      }
      stage('check') {
         steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'root-new', url: 'http://gitlab.dblace.com/dev/myblog.git']]])
         }
      }
      stage('build-image') {
         steps {
            retry(2) { sh 'docker build . -t myblog:latest'}
         }
      }
      stage('send-msg') {
         steps {
            sh """
            curl 'https://oapi.dingtalk.com/robot/send?access_token=4bf6701f9e1cd97b2366600e90b7ebf55928e681c9280e82f9afb8395e44eb81' \
   -H 'Content-Type: application/json' \
   -d '{"msgtype": "text", 
        "text": {
             "content": "我就是我, 是不一样的烟火"
        }
      }'
      """
         }
      }
   }
}

