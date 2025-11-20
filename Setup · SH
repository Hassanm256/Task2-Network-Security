#!/bin/bash

# Task 2: Network Security & Policy Development
# Firewall-Simulator Project - NetworkPolicies for Task 1 Applications
# This script secures Kbustos23's Task 1 infrastructure with zero-trust networking

echo " Task 2: Network Security & Policy Development"
echo "Integrating with Task 1's Firewall-Simulator infrastructure..."
echo ""

# Verify Task 1 is running
echo "Checking for Task 1 applications from Firewall-Simulator..."
if ! kubectl get pods -l app=frontend &> /dev/null; then
    echo " Task 1 applications not found!"
    echo "Please deploy Task 1 first: https://github.com/Kbustos23/Firewall-Simulator-"
    echo "Run: git clone https://github.com/Kbustos23/Firewall-Simulator-.git"
    echo "Then: cd Firewall-Simulator- && chmod +x setup.sh && ./setup.sh"
    exit 1
fi

echo " Task 1 applications detected!"
echo "Found Task 1 pods:"
kubectl get pods -l 'app in (frontend,backend,db)' -o wide

echo ""
echo "Found Task 1 services:"
kubectl get services -l 'app in (frontend,backend,db)'

# Install Calico CNI for advanced NetworkPolicies
echo ""
echo "Installing Calico CNI for advanced networking capabilities..."
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/tigera-operator.yaml
kubectl wait --for=condition=Available deployment/tigera-operator -n tigera-operator --timeout=300s

cat <<EOF | kubectl apply -f -
apiVersion: operator.tigera.io/v1
kind: Installation
metadata:
  name: default
spec:
  calicoNetwork:
    ipPools:
    - blockSize: 26
      cidr: 192.168.0.0/16
      encapsulation: VXLANCrossSubnet
      natOutgoing: Enabled
EOF

kubectl wait --for=condition=Ready pods -l k8s-app=calico-node -n calico-system --timeout=300s

# Apply NetworkPolicies to secure Task 1's applications
echo ""
echo "Applying NetworkPolicies to secure Task 1's infrastructure..."

echo "Applying: Default Deny All (Zero-Trust Foundation)"
kubectl apply -f default-deny-policy.yaml

echo "Applying: DNS Resolution Policy (Maintain Functionality)"
kubectl apply -f dns-policy.yaml

echo "Applying: Frontend External Access (Preserve NodePort)"
kubectl apply -f frontend-policy.yaml

echo "Applying: Backend Security Policy (Frontend → Backend Only)"
kubectl apply -f backend-policy.yaml

echo "Applying: Database Security Policy (Backend → Database Only)"
kubectl apply -f database-policy.yaml

echo ""
echo " Task 2: Network Security Setup Complete!"
echo ""
echo "NetworkPolicies Applied:"
kubectl get networkpolicy
echo ""
echo "Task 1's applications are now secured with zero-trust networking!"
echo ""
echo "Next Steps:"
echo "• Frontend still accessible: minikube service frontend-service --url"
echo "• Test security: Frontend should NOT directly access database"
echo "• Ready for Task 3 (Traffic Testing) and Task 4 (Monitoring)"
