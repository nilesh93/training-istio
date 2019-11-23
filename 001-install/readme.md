 curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.3.4 sh -
 cd istio-1.3.4
 
 helm repo add istio.io https://storage.googleapis.com/istio-release/releases/1.3.4/charts/
 kubectl create namespace istio-system
 ### rbac
 kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=$(gcloud config get-value core/account)


helm template install/kubernetes/helm/istio-init --name istio-init --namespace istio-system | kubectl apply -f -
kubectl get crds | grep 'istio.io' | wc -l

helm install install/kubernetes/helm/istio-cni --name=istio-cni --namespace=istio-system 



helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
helm install install/kubernetes/helm/istio --name istio --namespace istio-system

helm template install/kubernetes/helm/istio --name istio --namespace istio-system \
    --values install/kubernetes/helm/istio/values-istio-demo.yaml | kubectl apply -f -

### NOTE: Run the above again if it fails the first time
  
### Run demo apps
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml -n default
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml  -n default
kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml -n default
 
kubectl port-forward svc/productpage  9080  -n default 

kubectl apply -f samples/bookinfo/telemetry/metrics.yaml
kubectl apply -f samples/bookinfo/telemetry/tcp-metrics.yaml

kubectl port-forward svc/kiali 20001 -n istio-system
kubectl port-forward svc/grafana 3000 -n istio-system

 