eksctl create cluster --name=eksdemo --region=ap-south-1 --without-nodegroup

eksctl utils associate-iam-oidc-provider --region=ap-south-1 --cluster eksdemo --approve

eksctl create nodegroup --cluster=eksdemo --region=ap-south-1 --name=eksdemo-nodegroup --node-type=t2.large --nodes=1 --nodes-min=1 --nodes-max=2 --node-volume-size=20 --ssh-access --ssh-public-key=Pass --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access

eksctl scale nodegroup --cluster eksdemo --name eksdemo-nodegroup --nodes 2

kubectl -n kube-system describe configmap aws-auth

kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus --set server.persistentVolume.storageClass="gp2" --set serviceAccount.name=prometheus-ebs-sa  -n default

kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext

helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana

kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext

kubectl expose service prometheus-kube-state-metrics --type=NodePort --target-port=8080 --name=prometheus-kube-state-metrics-ext