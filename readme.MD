# Ingress Controller
*ADMINISTROTOR ACTIVITY

REGION_NAME=us-east-1
CLUSTER_NAME=roboshop
AWS_ACCOUNT_ID=753369745113

### PERMISSIONS
* OIDC Provider
'''
eksctl utils associate-iam-oidc-provider \
    --region $REGION_NAME \
    --cluster $CLUSTER_NAME\
    --approve
'''
* IAM Policy
'''
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v3.5.0/docs/install/iam_policy.json


'''
* CREATE IAM ROLE
'''
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
'''
* Provide access to EKS through IAM Policy
'''
eksctl create iamserviceaccount \
--cluster=$CLUSTER_NAME \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::$AWS_ACCOUNT_ID:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region $REGION_NAME \
--approve
'''

## INSTALL DRIVERS
* Add the EKS chart repo to Helm

sudo dnf install helm
'''
helm repo add eks https://aws.github.io/eks-charts
'''
* Install AWS Load Balancer controller
'''
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=$CLUSTER_NAME --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller


'''