
pipeline {
    agent any // 어느 젠킨스 서버에서나 실행 가능
    enviroment {
        SERVICE_DIRS = "config-service,discovery-service,gateway-service,course-service,eval-service,order-service,post-service,user-service"
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
                    def serviceDirs =  env.SERVICE_DIRS.split(",")
                    serviceDirs.each { service ->
                        sh """
                        echo "Building ${service}"
                        cd ${service}
                        ./gradlew clean build -x test
                        ls -al ./build/libs
                        cd ..
                        """
                    }
                }
              }
        }

    }
}