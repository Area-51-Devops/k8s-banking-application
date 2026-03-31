For installing CRD and prerequisites for K-gateway
Go to https://helm.sh/docs/intro/install (Debian/ubuntu Version)
Go to https://kgateway.dev/docs/envoy/latest/install/helm (Follow all steps)
Apply k-gateway and route yaml files
Do Get svc for kgateway and note the port
Create another node with less resources and install haproxy in it
steps:
1. sudo apt update
2. sudo apt install haproxy -y
3. sudo nano /etc/haproxy/haproxy.cfg
4. Add:
   frontend http_front
         mode http
         bind *80
         default_backend k8s_gateway
   backend k8s_gateway
         mode http
         balance roundrobin
         option forwardfor
         server worker-node-1 pubip/priip:port check (use the port of kgateway svc)
         server worker-node-2 pubip/priip:port check
5. sudo systemctl restart haproxy
6. sudo systemctl status haproxy
