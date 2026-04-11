eksctl create cluster --name=eksdemo --region=ap-south-1 --without-nodegroup

eksctl utils associate-iam-oidc-provider --region=ap-south-1 --cluster eksdemo --approve

eksctl create nodegroup --cluster=eksdemo --region=ap-south-1 --name=eksdemo-ng --node-type=t2.small --nodes=2 --nodes-min=1 --nodes-max=2 --node-volume-size=20 --ssh-access --ssh-public-key=Pass --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access

eksctl create iamserviceaccount --cluster=eksdemo --namespace=kube-system --name=aws-load-balancer-controller --attach-policy-arn=arn:aws:iam::975050063886:policy/AWSLoadBalancerControllerIAMPolicy --override-existing-serviceaccounts --approve

helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=eksdemo --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller --set region=ap-south-1 --set vpcId=vpc-071f5f377a73cbb03 --set image.repository=602401143452.dkr.ecr.ap-south-1.amazonaws.com/amazon/aws-load-balancer-controller

helm uninstall aws-load-balancer-controller -n kube-system

kubectl -n kube-system get sa aws-load-balancer-controller
kubectl -n kube-system get sa aws-load-balancer-controller -o yaml
kubectl -n kube-system get secret aws-load-balancer-controller-secret -o yaml

kubectl -n kube-system get secret aws-load-balancer-tls -o yaml

eksctl create iamserviceaccount --name external-dns --namespace default --cluster eksdemo --attach-policy-arn  --approve --override-existing-serviceaccounts