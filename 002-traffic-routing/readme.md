kubectl create ns training
kubectl label namespace training istio-injection=enabled

kubectl get namespace -L istio-injection


kubectl run nginx-test-svc1 --image nj93/istio-test-svc:v1 --port 80 --dry-run -o yaml --labels "app=nginx,version=v1" > nginx-v1.yaml > nginx-test-v1.yaml
kubectl run nginx-test-svc2 --image nj93/istio-test-svc:v2 --port 80 --dry-run -o yaml --labels "app=nginx,version=v2" > nginx-v1.yaml > nginx-test-v2.yaml

### NOTE: need to make some changes to the original yaml to add readiness probes

### NOTE: To run Istio, you need a VM with more than 2vCPU

### NOTE: It is essential that you have a readiness probe defined in your container. If not, istio-proxy will not start. 
###       If you don't want readiness check enabled, add the following as a pod annotation
###       readiness.status.sidecar.istio.io/applicationPorts: "" 

kubectl apply -f nginx-test-v1.yaml 
kubectl apply -f nginx-test-v2.yaml

### TEST: check if the labels are okay
kubectl get pods --show-labels

### NOTE: For the Virtual Service to connect, it requires a Service exposed

kubectl expose deploy nginx-test-svc1  --target-port 80 --port 80 --dry-run -o yaml --type=ClusterIP > nginx-test-svc.svc.yaml

### NOTE: Edit the service to make sure the service points to both the deployments using a common label
### NOTE: Services should have named ports
kubectl apply -f nginx-test-svc.svc.yaml 

### TEST: Make sure the service have 2 endpoints created.
kubectl describe svc nginx-test

### TEST: run a busybox on a seperate terminal and in the SAME namespace and exec in and do the following

kubectl run busybox --image busybox:1.27 --rm --restart Never -it -- /bin/sh
wget -O- nginx-test

### EXPLAIN: The traffic flow
Virtual Service --> Destination Rule ---> Service ---> Pod

### EXPLAIN: destination rules group different pod versions with labels
kubectl apply -f destination-rule-nginx-test.yaml

### EXPLAIN: destination rules group different pod versions with labels
kubectl apply -f virtual-service-nginx-test-svc.yaml


### EXPLAIN: canaries with virtual service
### EXPLAIN: headers with virtual service
 wget -O- --header='baggage-user-agent: Safari' nginx-test
### EXPLAIN: mirror (dark launch) with virtual service

### REF: https://blog.christianposta.com/microservices/advanced-traffic-shadowing-patterns-for-microservices-with-istio-service-mesh/


### NOTE: to debug istio routing use the following

kubectl delete job -n istio-system istio-vet
kubectl apply -f https://raw.githubusercontent.com/aspenmesh/istio-vet/master/install/kubernetes/istio-vet.yaml
kubectl -n istio-system logs -l "app=istio-vet" --tail=0


kubectl exec -it busybox -- 