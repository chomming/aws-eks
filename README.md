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
$ eksctl utils associate-iam-oidc-provider --cluster ${CLUSTER_NAME} --approve

## IAM policy 생성하기
$ curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.7/docs/install/iam_policy.json
$ aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://iam_policy.json

## IAM role 생성하기
$ POLICY_ARN=$(aws iam list-policies --query 'Policies[?PolicyName==`AWSLoadBalancerControllerIAMPolicy`].Arn' --output text)
$ ROLE_NAME="AmazonEKSLoadBalancerControllerRole"
$ CLUSTER_NAME="${eks-cluster-name}"

## kubernetes serviceaccount 생성하기
$ eksctl create iamserviceaccount \
--cluster ${CLUSTER_NAME} \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--role-name ${ROLE_NAME} \
--attach-policy-arn=${POLICY_ARN} \
--approve

## AWS ALB controller 설치하기
$ helm repo add eks https://aws.github.io/eks-charts
$ helm repo update
$ helm upgrade --install aws-load-balancer-controller eks/aws-load-balancer-controller \
-n kube-system \
--set clusterName=${CLUSTER_NAME} \
--set serviceAccount.create=false \
--set serviceAccount.name=aws-load-balancer-controller

## kube-system namespace에 ALB Controller pod가 실행 중인지 확인하기
$ kubectl -n kube-system get po

## NLB 생성하기
$ vi nlb.yaml \
apiVersion: apps/v1 \
kind: Deployment \
metadata: \
&nbsp;&nbsp;name: tomcat-nlb \
spec: \
  selector: \
    matchLabels: \
      app: tomcat-nlb \
  replicas: 1 \
  template: \
    metadata: \
      labels: \
        app: tomcat-nlb \
    spec: \
      containers: \
        - name: tomcat \
          image: tomcat:latest \
          ports: \
            - containerPort: 8080 \
--- \
apiVersion: v1 \
kind: Service \
metadata: \
  name: tomcat-nlb \
  annotations: \
    service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: ip \
    service.beta.kubernetes.io/aws-load-balancer-scheme: internet-facing \
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-port: "8080" \
spec: \
  type: LoadBalancer \
  loadBalancerClass: service.k8s.aws/nlb \
  selector: \
    app: tomcat-nlb \
  ports: \
    - port: 80 \
      targetPort: 8080 \
      protocol: TCP \











