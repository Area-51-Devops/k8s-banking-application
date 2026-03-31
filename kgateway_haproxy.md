1. For installing CRD and prerequisites for K-gateway
2. Go to https://helm.sh/docs/intro/install (Debian/ubuntu Version)
3. Go to https://kgateway.dev/docs/envoy/latest/install/helm (Follow all steps)
4. Apply k-gateway and route yaml files
5. Do Get svc for kgateway and note the port
6. Create another node allowing port 80 and ssh from anywhere and install haproxy in it.
Follow this steps:
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
