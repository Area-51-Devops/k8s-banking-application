🚀 K-Gateway Setup with HAProxy Load Balancer

This guide walks through installing K-Gateway using Helm, deploying it on Kubernetes, and exposing it externally using HAProxy.

📌 Prerequisites
Kubernetes cluster (with at least 2 worker nodes)
kubectl configured
helm installed
Debian/Ubuntu-based VM for HAProxy
Internet access on all nodes
📦 Step 1: Install Helm (Debian/Ubuntu)

Follow official Helm installation:

curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

Verify installation:

helm version
⚙️ Step 2: Install K-Gateway CRDs & Prerequisites

Add Helm repo:

helm repo add kgateway https://kgateway.dev/charts
helm repo update

Install CRDs:

helm install kgateway-crds kgateway/kgateway-crds
🚀 Step 3: Install K-Gateway
helm install kgateway kgateway/kgateway

Verify pods:

kubectl get pods -A
📄 Step 4: Apply K-Gateway & Route YAMLs

Apply your gateway and route configurations:

kubectl apply -f kgateway.yaml
kubectl apply -f route.yaml
🔍 Step 5: Get K-Gateway Service Port

Retrieve the service details:

kubectl get svc -n default

Note the NodePort / Service Port of the K-Gateway service.

Example:

NAME        TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)
kgateway    NodePort   10.0.0.12      <none>        80:30007/TCP

👉 Use 30007 (NodePort) for HAProxy configuration.

🌐 Step 6: Setup HAProxy Node

Create a new VM/node with:

Port 80 open
SSH access enabled
🛠️ Step 7: Install HAProxy
sudo apt update
sudo apt install haproxy -y
✏️ Step 8: Configure HAProxy

Edit config file:

sudo nano /etc/haproxy/haproxy.cfg

Add the following:

frontend http_front
    mode http
    bind *:80
    default_backend k8s_gateway

backend k8s_gateway
    mode http
    balance roundrobin
    option forwardfor
    server worker-node-1 <PUBLIC_OR_PRIVATE_IP>:<NODEPORT> check
    server worker-node-2 <PUBLIC_OR_PRIVATE_IP>:<NODEPORT> check
🔧 Example
server worker-node-1 192.168.1.10:30007 check
server worker-node-2 192.168.1.11:30007 check
🔄 Step 9: Restart HAProxy
sudo systemctl restart haproxy

Check status:

sudo systemctl status haproxy
✅ Step 10: Test the Setup

Open browser:

http://<HAProxy-Public-IP>

You should see responses routed via K-Gateway.

📊 Architecture Overview
Client Request
      ↓
 HAProxy (Port 80)
      ↓
------------------------
| Worker Node 1       |
| Worker Node 2       |
------------------------
      ↓
   K-Gateway
      ↓
 Kubernetes Services
🧩 Notes
Ensure NodePort is accessible from HAProxy node
Use private IPs if nodes are in same VPC
Use public IPs if across networks
Security groups/firewalls must allow NodePort traffic
🛑 Troubleshooting
Check HAProxy logs:
sudo journalctl -u haproxy -f
Verify backend connectivity:
curl http://<node-ip>:<nodeport>
Check Kubernetes services:
kubectl get svc
kubectl get pods
🎯 Conclusion

You now have:

K-Gateway deployed via Helm
Traffic exposed via NodePort
HAProxy acting as external load balancer


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
