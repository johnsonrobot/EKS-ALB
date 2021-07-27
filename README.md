### 1. Enable OIDC provider in your cluster 
> eksctl utils associate-iam-oidc-provider --cluster test2 --approve
### 2. Create cluster role
> kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/rbac-role.yaml
### 3. Attach IAM policy about AWSLoadBalancerControllerIAMPolicy in your role
> curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.2.0/docs/install/iam_policy.json

> aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

> eksctl create iamserviceaccount \
  --cluster=test2 \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::"account number":policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --approve

### 4. Install cert-manager in your cluster 
> kubectl apply \
--validate=false \
-f https://github.com/jetstack/cert-manager/releases/download/v1.1.1/cert-manager.yaml

### 5. Deploy Ingress Controller
- Delete the ServiceAccount section of the file (v2_2_0_full.yaml)
- Replace your-cluster-name in the file.
> curl -o v2_2_0_full.yaml https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.2.0/docs/install/v2_2_0_full.yaml

> kubectl apply -f v2_2_0_full.yaml
### 6. Deploy Ingress Application
> kubectl apply -f ingress.yaml
* execute the above command. you will see ALB in your aws console

> kubectl apply -f web-deploy.yaml