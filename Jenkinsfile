pipeline {
  agent any
  // any, none, label, node, docker, dockerfile, kubernetes
  tools{
    maven 'my_maven'
  }

  environment  {
    gitName = 'dduddu00'
    gitEmail = 'tndusdlsms@nate.com'
    gitWebaddress = 'https://github.com/dduddu00/sb_code.git'
    gitSshaddress = 'git@github.com:dduddu00/sb_code.git'
            //위 주소 두 개는 git에서 초록색 code 누르면 나옴 (HTTPS, SSH)
    gitCredential = 'git_cre' //git Credential 생성시의 ID
    dockerHubRegistry = 'choisooyeon/sbimage' 
    dockerHubRegistryCredential = 'docker_cre' // dcker Credential 생성시의 ID
  }

  stages {
    stage('Checkout Github') {
        steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: gitCredential, url: gitWebaddress]]])
            // 코드 빌드 시 어디에서 받아올 것인지 정함
        }
        post {
            //성공 또는 실패했을 경우 명령이 출력되도록 만들기
            failure {
                echo 'Repository Clone Failure'
            }
            success {
                echo 'Repository Clone Success'
            }
        }
    }
    stage('maven Build') {
        steps {
            sh 'mvn clean install'
            //maven 플러그인이 미리 설치되어 있어야 한다.
        }
        post {
            failure {
                echo 'maven build failure'
            }
            success {
                echo 'maven build success'
            }
        }
    }
    stage('Docker image Build') {
        steps {
            sh "docker build -t ${dockerHubRegistry}:${currentBuild.number} ."
            sh "docker build -t ${dockerHubRegistry}:latest ."
            // choisooyeon/sbimage:1 이런식으로 빌드가 될 것이다. 
            // currentBuild.number => 젠킨스에서 제공하는 빌드넘버 변수
        }
        post {
            failure {
                echo 'docker image build failure'
            }
            success {
                echo 'docker image build success'
            }
        }
    }
    stage('Docker image push') {
        steps {
            withDockerRegistry(credentialsId: dockerHubRegistryCredential, url: '') {
                // withDockerRegistry : docker pipeline 플러그인 설치시 사용가능.
                // dockerHubRegistryCredential : environment에서 선언한 docker_cre  
                sh "docker push ${dockerHubRegistry}:${currentBuild.number}"
                sh "docker push ${dockerHubRegistry}:latest"
            }
        }
        post {
            failure {
                echo 'docker image push failure'
            }
            success {
                echo 'docker image push success'
            }
        }
    }
    }
}
