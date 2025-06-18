
pipeline {
    agent any // 어느 젠킨스 서버에서나 실행 가능
    environment  {
        SERVICE_DIRS = "config-service,discovery-service,gateway-service,course-service,eval-service,order-service,post-service,user-service"
    }
    stages {
        stage('Pull Codes from Github') {
            steps {
                checkout scm // 젠킨스와 연결된 소스를 가져오는 명령어
            }


        }
        stage('Detect Changes') {
            steps {
                script {
                    // 변경된 파일 감지
                    def changedFiles = sh(script: "git diff --name-only HEAD~1 HEAD", returnStdout: true)
                                        .trim()
                                        .split('\n') // 변경된 파일을 줄 단위로 분리

                    // 변경된 파일 출력
                    echo "Changed Files: ${changedFiles}"

                    def changedService = []
                    def serviceDirs = env.SERVICE_DIRS.split(",")

                    serviceDirs.each { service ->
                        // changedFiles 이라는 리스트를 조회해서 service 변수에 들어온 서비스 이름과
                        // 하나라도 일치하는 이름이 있다면 true, 하나도 존재하지 않으면 false
                        // service: user-service -> 변경된 파일 경로가 user-service/로 시작한다면 true
                        if (changedFiles.any {it.startsWith(service+"/")}) {
                            changedService.add(service)
                        }
                    }

                    // 변경된 서비스이름을 모아놓은 리스트를 다른 스테이지에서도 사용하기 위해 환경 변수로 선언
                    env.CHANGED_SERVICES = changedService.join(",")
                    if (env.CHANGED_SERVICES == "") {
                        echo "No change detected in service directories. Skipping build and deployment."
                        // 성공 상태로 파이프라인 종료
                        currentBuild.result = 'SUCCESS'
                    }
                }
            }
        }
        stage('Build Changed Services') {
            // 이 스테이지는 빌드되어야 할 서비스가 존재한다면 실행되는 스테이지
            // 이전 스테이지에서 세팅한 CHANGED_SERVICE라는 환경변수가 비어있지 않아야 실행됨
            when {
                expression { env.CHANGED_SERVICES != "" }
            }
            steps {
                script {
                    def changedServices =  env.CHANGED_SERVICES.split(",")
                    changedServices.each { service ->
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