# k8-ingress

REGION_CODE=us-east-1 CLUSTER_NAME=expense ACC_ID=484907497033

# Permissions
* OIDC provider

eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster expense-1 \
    --approve

* IAM Policy

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.10.0/docs/install/iam_policy.json

* Create IAM Policy

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json

* Provide access to EKS through IAM Policy

eksctl create iamserviceaccount \
--cluster=expense-1 \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::484907497033:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve

# Install Drivers
* Add the EKS chart repo to Helm

helm repo add eks https://aws.github.io/eks-charts

* Install AWS Load Balancer Controller

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=expense-1 --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller