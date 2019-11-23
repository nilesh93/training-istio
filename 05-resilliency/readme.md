### EXPLAIN: LoadBalancing - 
### NOTE: done in Destination Rule
ROUND_ROBIN -  evenly distributes the load,in order
RANDOM -evenly distributes the load across the endpoints in the load-balancing pool but without any order.
LEAST_CONN - picks two random hosts from the load-balancing pool and determines which host has fewer outstanding requests
### NOTE: Traffic policy can be created common to all subsets or per subset

### SIEGE. Tool for load testing
kubectl run siege --rm --image lopezs/siege -it --restart Never -- -r 2 -c 20 -v <dns>  
https://hub.docker.com/r/lopezs/siege/

### EXPLAIN: Timeout
### NOTE: Done in Virtual Service

### EXPLAIN: Retry
### NOTE: Done in Virtual Service

This rule sets your retry attempts to 3 and will use a 2s timeout for each retry. The cumulative timeout is therefore 6 seconds plus the time of the original call.


### EXPLAIN: Circuit Breaker
### NOTE: done in Destination Rule
You are limiting the number of connections and number of pending requests to one. IF there are any requests that gets hung for a certain time, the traffic gets routed to other pods rather than push more requests to the same pod if it's pending. The goal of the circuit breaker is to fail fast

### EXPLAIN: Pool Ejection
### NOTE: done in Destination Rule
### Connection pool has no rules
Take the pods away from the loadbalancing list for a certain time

In this DestinationRule, you’re configuring Istio to check every 5 seconds for misbehaving pods and to remove those pods from the load-balancing pool after one consecutive error (artificially low for this example) and keep it out for 15 seconds

### Combination: Circuit Breaker + Pool Ejection + Retry