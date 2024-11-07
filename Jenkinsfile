pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS_ID = 'docker'  // Docker Hub 자격증명 ID
        GITHUB_CREDENTIALS_ID = 'github'  // GitHub 자격증명 ID
        DOCKER_IMAGE = 'dongjukim123/nodejs-ci'  // Docker 이미지 이름
        IMAGE_TAG = 'latest'  // 원하는 이미지 태그
    }

    stages {
        stage('Checkout') {
            steps {
                // Git 리포지토리에서 소스 코드 체크아웃
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/dongjucloud/nodejs-ci',  // GitHub 저장소 URL로 대체
                        credentialsId: GITHUB_CREDENTIALS_ID
                    ]]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                // Docker 이미지를 빌드하고 태그 추가
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                // Docker Hub에 로그인하고 이미지를 푸시
                script {
                    docker.withRegistry('', DOCKER_HUB_CREDENTIALS_ID) {
                        sh "docker push ${DOCKER_IMAGE}:${IMAGE_TAG}"
                    }
                }
            }
        }
    }

    post {
        always {
            // 빌드가 완료된 후 작업 디렉토리에서 Docker 이미지를 정리
            sh "docker rmi ${DOCKER_IMAGE}:${IMAGE_TAG} || true"
        }
        success {
            echo 'Build and deployment succeeded!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}

