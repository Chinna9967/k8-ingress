### iNGRESS CONTROLLER

Reference

https://kubernetes-sigs.github.io/aws-loadbalancer-controller/v2.6/

1. Create an IAM OIDC(authentication between eks and aws) provider. You can skip this step if you already have one for your cluster.
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster eks-spot-cluster \
    --approve
2. If your cluster is in any other region:
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.6.1/docs/install/iam_policy.json

3. Create an IAM policy named AWSLoadBalancerControllerIAMPolicy. If you downloaded a different policy, replace iam-policy with the name of the policy that you downloaded.

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json

4. Create an IAM role and Kubernetes ServiceAccount for the LBC. Use the ARN from the previous step.

eksctl create iamserviceaccount \
--cluster=eks-spot-cluster \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::127147268179:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve

5. install HELM charts

    1. Add the EKS chart repo to Helm

    helm repo add eks https://aws.github.io/eks-charts
    2. If upgrading the chart via helm upgrade, install the TargetGroupBinding CRDs.

    helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=eks-spot-cluster --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
