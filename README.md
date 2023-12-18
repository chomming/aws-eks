## EKS
- container 예약, 애플리케이션 가용성 관리, 클러스터 데이터 저장 및 다른 주요 작업을 담당하는 kubernetes 컨트롤 플레인의 가용성, 확장성 관리
- AWS 네트워킹 및 보안 서비스 + AWS 인프라의 모든 성능, 규모, 신뢰성 및 가용성 활용 가능
- 자체 kubernetes 컨트롤 플레인, 작업자 노드 설치 및 운영할 필요 없음
- 장점
  - ECS에 비해 서비스에 대한 자유도 높음
  - 다른 클라우드 서비스로 이전할 때 용이함
  - yaml 파일로 인프라 구축 가능
- 단계
  - 1단계 : 클러스터 생성
    - eksctl, AWS Management Console, AWS CLI, AWS SDK 중 하나를 사용하여 클러스터 생성
  - 2단계 : 컴퓨팅 리소스에 대한 접근 방식 선택
    - AWS Fargate, Karpenter, 관리형 노드 그룹, 자체 관리형 노드 중 결정
  - 3단계 : 설정
    - 필요한 컨트롤러, 드라이버, 서비스 설정
  - 4단계 : 워크로드 배포
    - 선택한 노드 유형의 리소스, 기능을 잘 활용하도록 Kubernetes 워크로드 조정
  - 5단계 : 관리
    - 워크로드 감독하여 AWS 서비스 통합
    - 운영 간소화
    - 워크로드 성능 향상
    - AWS Management Console 사용하여 워크로드에 대한 정보 볼 수 있음

## 클러스터 IAM OIDC 생성하기
$ eksctl utils associate-iam-oidc-provider --cluster cmk-eks-cluster --approve

