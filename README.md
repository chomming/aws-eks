## 전반적인 과정-1 Docker 관련
- Dockerfile 생성하기
  - [Dockerfile](https://github.com/chomming/aws-eks/blob/main/Dockfile)
- Docker 클라이언트 인증하기
  - aws ecr get-login-password --region <리전 이름> | docker login --username AWS --password-stdin <계정 ID>.dkr.ecr.<리전 이름>.amazonaws.com
- Docker 이미지 빌드하기
  - docker build -t <이미지 이름>:<태그> <Dockerfile 경로>
- ECR로 푸시하기
  - docker tag <리포지토리 이름>:latest <계정 ID>.dkr.ecr.<리전 이름>.amazonaws.com/<리포지토리 이름>:latest
- AWS 리포지토리로 푸시하기
  - docker push <리전 이름>.dkr.ecr.<리전 이름>.amazonaws.com/<리포지토리 이름>:latest
- Docker 컨테이너 실행하기
  - docker run --name <컨테이너 이름> <이미지 이름>:<태그>
 
## 전반적인 과정-2 Yaml 관련
- deployment.yaml 파일
  - [deployment.yaml](https://github.com/chomming/aws-eks/blob/main/deployment.yaml)
- service.yaml 파일
  - [service.yaml]
- 
