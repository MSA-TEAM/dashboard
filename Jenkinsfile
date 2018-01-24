node {

    stage ('소스체크아웃') {
        checkout([$class: 'GitSCM',
            branches: [[name: '*/master']],
            doGenerateSubmoduleConfigurations: false, extensions: [],
            submoduleCfg: [],
            userRemoteConfigs: [[credentialsId: 'DevPro', url: 'https://devpro.ktds.co.kr/msa/dashboard.git']]])
    }

    stage ('빌드') {
        sh './gradlew clean build'
    }

    stage ('스크립팅'){
        sh 'ssh ec2-user@ip-172-31-6-72 "mkdir -p /home/ec2-user/dashboard/log"'
        sh 'scp ./start.sh ec2-user@ip-172-31-6-72:/home/ec2-user/dashboard'
        sh 'scp ./shutdown.sh ec2-user@ip-172-31-6-72:/home/ec2-user/dashboard'
        sh 'ssh ec2-user@ip-172-31-6-72 "chmod a+x /home/ec2-user/dashboard/*.sh"'
    }

    stage ('서버 중지'){
        sh 'ssh ec2-user@ip-172-31-6-72 "/home/ec2-user/dashboard/shutdown.sh || true"'
    }

    stage ('배포') {
        sh 'scp build/libs/dashboard-1.0.0-RELEASE.jar ec2-user@ip-172-31-6-72:/home/ec2-user/dashboard'
    }

    stage ('서버 시작') {
        sh 'ssh ec2-user@ip-172-31-6-72 "/home/ec2-user/dashboard/start.sh /home/ec2-user/dashboard/dashboard-1.0.0-RELEASE.jar prd"'
    }

}