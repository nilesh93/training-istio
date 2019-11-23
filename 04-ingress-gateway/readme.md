### EXPLAIN: The traffic flow
Istio Gateway Service (Ingress Service) --> Gateway --> Virtual Service --> Destination Rule ---> Service ---> Pod

curl -I -HHost:nginx.example.com  http://$INGRESS_HOST:$INGRESS_PORT
curl  -HHost:nginx.example.com  http://$INGRESS_HOST:$INGRESS_PORT

### NOTE: Gateway resource and istio gateway service doesnt need to be on the same ns