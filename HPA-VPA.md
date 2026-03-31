HPA and VPA Setup Prerequisites
 
1. Check metrics server
   
   kubectl get deployment metrics-server -n kube-system
 
2. If metrics server is not installed, install it
   
   kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
 
3. Verify node metrics
   
   kubectl top nodes
 
4. Verify pod metrics
   
   kubectl top pods -n banking-app
 
5. Install VPA
    
    git clone https://github.com/kubernetes/autoscaler.git

    cd autoscaler/vertical-pod-autoscaler

    ./hack/vpa-up.sh
 
7. Apply HPA YAML files
    
    kubectl apply -f autoscaling/hpa/
 
8. Apply VPA YAML files
    
    kubectl apply -f autoscaling/vpa/
 
9. Verify HPA
    
    kubectl get hpa -n banking-app
 
10. Verify VPA
    
    kubectl get vpa -n banking-app
 
11. Check autoscaling status
    
    kubectl top pods -n banking-app

    kubectl get pods -n banking-app
 
13. Check scaling
    
    kubectl get hpa -n banking-app -w
 
