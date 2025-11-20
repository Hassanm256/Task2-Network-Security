# Task2-Network-Security
Network Security &amp; Policy Development for Firewall Simulator Project
# Task2-Network-Security

This repository contains the network security implementation for our Kubernetes NetworkPolicy firewall project. The goal is to secure Task 1's multi-tier microservice application from [Firewall-Simulator](https://github.com/Kbustos23/Firewall-Simulator-) with Calico NetworkPolicies, implementing a zero-trust network environment.

##  2. Network Security & Policy Dev 

### Prerequisites
You must have the following installed and running:
- Task 1 infrastructure deployed (from [Kbustos23/Firewall-Simulator-](https://github.com/Kbustos23/Firewall-Simulator-))
- Minikube (Running with Docker driver)
- kubectl (Installed via Homebrew/Chocolatey)

### Setup Instructions
Follow these steps to add NetworkPolicies to Task 1's deployed applications:

**Clone the Repository:**
```bash


**Run the Setup Script:**
The script will install Calico CNI and apply all 5 NetworkPolicy manifest files to secure Task 1's applications.
```bash
chmod +x setup.sh
./setup.sh
```

### Verification (Confirm Security Working)
Once the script completes, verify the security policies are securing Task 1's apps:

**Check Network Policies:**
Ensure all 5 policies are active:
```bash
kubectl get networkpolicy
```

**Access Frontend:**
The Web UI should still be accessible at the same URL from Task 1:
```bash
minikube service frontend-service --url
```

**Test Zero-Trust Security:**
Verify that direct database access is blocked (this should fail):
```bash
kubectl exec [frontend-pod-name] -- timeout 5 nc -zv [db-pod-ip] 6379
```

##  2. Enhanced App Architecture
Our NetworkPolicies secure Task 1's application stack using their exact labels and services.

| Application Pod | Task 1 Label (app) | Task 1 Tier Label (tier) | Function |
|-------------|-------------|----------------|----------|
| Frontend | app: frontend | tier: web | Public-facing service, only accepts external traffic |
| Backend | app: backend | tier: application | API service, only accepts traffic from Frontend |
| Database | app: db | tier: data | Redis database, only accepts traffic from Backend |

##  3. NetworkPolicy Security Diagram

```
Internet → Frontend(app:frontend) → Backend(app:backend) → Database(app:db)
           tier:web              tier:application       tier:data
NodePort       ↓                        ↓                   ↓
(Port 80)   ClusterIP                ClusterIP          ClusterIP
           (Port 80)                (Port 80)          (Port 6379)

Task 2 Security Rules Applied:
Internet → Frontend:80 (allowed - NodePort service)
Frontend → Backend:80 (allowed - controlled access)  
Backend → Database:6379 (allowed - controlled access)
All pods → DNS:53 (allowed - service discovery)
Frontend → Database:6379 (BLOCKED - zero-trust security)
All other traffic (BLOCKED - default deny)
```

##  NetworkPolicy Integration

### Task 2 NetworkPolicies Applied to Task 1 Infrastructure:
1. **default-deny-policy.yaml** - Blocks all traffic by default (zero-trust foundation)
2. **dns-policy.yaml** - Allows DNS resolution for all pods (maintains functionality)
3. **frontend-policy.yaml** - Allows external access to frontend (preserves NodePort access)
4. **backend-policy.yaml** - Allows frontend → backend communication only
5. **database-policy.yaml** - Allows backend → database communication only

### Perfect Integration:
- Uses Task 1's exact pod labels: `app: frontend`, `app: backend`, `app: db`
- Uses Task 1's exact tier labels: `tier: web`, `tier: application`, `tier: data`  
- Maintains Task 1's service functionality while adding security
- Zero-trust architecture prevents lateral movement attacks

##  Team Collaboration

**Task 1 + Task 2 Combined:**
- **Task 1 (Kbustos23):** Provides infrastructure and applications
- **Task 2 (Hassanm256):** Adds enterprise-grade network security
- **Result:** Production-ready secure microservice environment

**For Tasks 3 & 4:**
- **Task 3:** Can use these policies for traffic testing and validation
- **Task 4:** Can monitor policy effectiveness and security metrics

---

