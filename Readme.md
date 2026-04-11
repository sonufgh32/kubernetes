eksctl create cluster --name=eksdemo --region=ap-south-1 --without-nodegroup

eksctl utils associate-iam-oidc-provider --region=ap-south-1 --cluster eksdemo --approve

eksctl create nodegroup --cluster=eksdemo --region=ap-south-1 --name=eksdemo-ng --node-type=t2.medium --nodes=2 --nodes-min=1 --nodes-max=2 --node-volume-size=20 --ssh-access --ssh-public-key=Pass --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access

kubectl -n kube-system describe configmap aws-auth

kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"

kubectl run -it --rm --image=mysql:5.6 --restart=Never mysql-client -- mysql -h mysql.cvmw6sqa03x4.ap-south-1.rds.amazonaws.com
s
kubectl exec -it mysql-548576f7b8-zr2n8 -- mysql -h -u root -p