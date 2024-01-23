# EKS


## Deploy 2048 game from scratch

### Prerequisites:
OS: Windows

1. Install kubectl on local system for Windows.
2. Install eksctl on local system for Windows.
3. Install AWS CLI on local system for Windows.

4. Configure AWS CLI using: aws configure -> provide access key and secret key for root user of AWS account(keep region as default region and Output as text).
   Can be found in Your AWS Account -> Security Credentials -> Access Keys
   <img width="949" alt="image" src="https://github.com/Neelansh01/EKS/assets/39853942/c4dbdaa7-f474-4a0e-bfda-764a2f6fb964">


### Create Cluster
5. Create cluster using eksctl: eksctl create cluster --name demo-cluster --region us-east-1 --fargate
   <img width="943" alt="image" src="https://github.com/Neelansh01/EKS/assets/39853942/515b6894-ebf9-4533-a5a3-95b956ffbb4e">

7. Add the cluster to command line: aws eks update-kubeconfig --name demo-cluster --region us-east-1

## Create new Fargate profile with new namespace
7. eksctl create fargateprofile \
    --cluster demo-cluster \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048
<img width="946" alt="image" src="https://github.com/Neelansh01/EKS/assets/39853942/e67cf2d7-8054-4e49-90cc-5083261b61c4">

8. Deploy deployment, ingress and services:
   kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml

### Check if pods are created
9. kubectl get pods -n game-2048
10. kubectl get svc -n game-2048
11. kubectl get ingress -n game-2048

### Create Load Balancer
12. Install OIDC Connector: eksctl utils associate-iam-oidc-provider --cluster demo-cluster --region us-east-1 --approve
13. Download IAM policy: curl -O -k https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
14. Create IAM Policy: aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
15. Create IAM Role: eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> --region us-east-1\
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

### Install and Update Helm
16. Install Helm on local system for Windows.
17. Add helm repo: helm repo add eks https://aws.github.io/eks-charts
18. Update the repo: helm repo update eks
19. Install: helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
  -n kube-system \
  --set clusterName=<your-cluster-name> --region us-east-1\
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=<your-vpc-id>
20. Verify if deployment is running: kubectl get deployment -n kube-system aws-load-balancer-controller

### Run the Game
21. Copy the address from the output of: kubectl get ingress -n game-2048.
22. Run the address as: http://<address>
<img width="958" alt="image" src="https://github.com/Neelansh01/EKS/assets/39853942/3a26c0d0-fa7c-4748-b4cf-89a4cab0a2b6">










### OPTIONAL





