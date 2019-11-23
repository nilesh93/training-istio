### NOTE: by default block all outbound traffic unless you allow explicitly
can be used in both zero trust architecture and traditional perimeter based security

### NOTE GKE Istio comes with allow any
kubectl get configmap istio -n istio-system -o yaml | grep -o "mode: ALLOW_ANY"

### NOTE: 2 options: REGISTRY_ONLY, ALLOW_ANY

###  ALLOW_ANY --> REGISTRY_ONLY
 kubectl get configmap istio -n istio-system -o yaml | sed 's/mode: ALLOW_ANY/mode: REGISTRY_ONLY/g' | kubectl replace -n istio-system -f -

###  REGISTRY_ONLY --> ALLOW_ANY
 kubectl get configmap istio -n istio-system -o yaml | sed 's/mode: REGISTRY_ONLY/mode: ALLOW_ANY/g' | kubectl replace -n istio-system -f -

### NOTE: to do this, you have to enable istio gateway
https://istio.io/docs/tasks/traffic-management/egress/egress-gateway/


wget -O- https://httpbin.org/headers

### NOTE: Similar to inter cluster requests, external links too can have route rules through virtual service.
### NOTE: ServiceEntry replaces DestinationRule
