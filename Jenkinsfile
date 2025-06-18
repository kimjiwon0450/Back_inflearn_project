
pipeline {
    agent any
    enviroment {
        // 환경 변수 선언

    }
    stages {
        stage('Pull Codes from Github') {
            steps {
                checkout scm // 젠킨스와 연결된 소스를 가져오는 명령어
            }


        }
        stage('Build Codes by Gradle') {
              steps {
                script {
                    sh """
                    echo "Build Stage Start!"
                    """
                }
              }
        }

    }
}