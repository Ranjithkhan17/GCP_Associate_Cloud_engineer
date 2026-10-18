# Managing Deployments Using Google Kubernetes Engine (GKE)

This guide shows step-by-step instructions to manage Kubernetes deployments on GKE. Commands are shown in bash blocks so you can copy/paste them into your terminal. Replace values (project, image paths) as needed for your environment.

## Prerequisites
- gcloud and kubectl installed and authenticated.
- Billing & GKE API enabled for your Google Cloud project.
- Access to the sample manifests in the repository (deployments/ and services/ directories).

---

## 1. Get the sample code for this lab

Use gsutil (or gcloud storage) to copy the sample manifests to your local machine and change into the directory:

```bash
# Using gsutil
gsutil -m cp -r gs://spls/gsp053/kubernetes .
cd kubernetes

# Or using gcloud storage (newer alternative)
gcloud storage cp -r gs://spls/gsp053/kubernetes .
cd kubernetes
```

## 2. Create a GKE cluster with 3 nodes

Create a cluster named `bootcamp` using a small machine type (adjust as needed):

```bash
gcloud container clusters create bootcamp \
  --machine-type e2-small \
  --num-nodes 3 \
  --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"

# Get credentials for kubectl (if gcloud didn't do it automatically):
gcloud container clusters get-credentials bootcamp
```

Verify cluster and nodes:

```bash
kubectl get nodes
```

## 3. Create the initial (blue) deployment

Apply the blue deployment manifest (idempotent):

```bash
kubectl apply -f deployments/fortune-app-blue.yaml
```

Confirm the deployment and pods are running:

```bash
kubectl get deployments
kubectl get pods -l app=fortune-app -o wide
```

## 4. Expose the deployment with a Service

Create a service to expose the fortune-app deployment:

```bash
kubectl apply -f services/fortune-app.yaml

# Verify the service
kubectl get svc fortune-app
```

---

## 5. Scale the deployment

Scale up to 5 replicas:

```bash
kubectl scale deployment fortune-app-blue --replicas=5
kubectl get deployment fortune-app-blue -o wide
```

Scale back to 3 replicas:

```bash
kubectl scale deployment fortune-app-blue --replicas=3
kubectl get deployment fortune-app-blue -o wide
```

---

## Rolling updates

A rolling update replaces old pods with new pods incrementally so your service stays available.

Option A — preferred: update the image using `kubectl set image` (safe & scriptable):

```bash
kubectl set image deployment/fortune-app-blue fortune-service=us-central1-docker.pkg.dev/qwiklabs-resources/spl-lab-apps/fortune-service:2.0.0
kubectl rollout status deployment/fortune-app-blue
```

Option B — interactive: edit the deployment to change image and env var:

```bash
kubectl edit deployment fortune-app-blue
# In the editor: change the image tag from :1.0.0 to :2.0.0
# Also update APP_VERSION env var from "1.0.0" to "2.0.0"
# Save and exit (e.g., Esc, :wq in vi)
kubectl rollout status deployment/fortune-app-blue
```

Check rollout history and status:

```bash
kubectl rollout history deployment/fortune-app-blue
kubectl rollout status deployment/fortune-app-blue
```

### Pause and resume a rollout

Pause an in-progress rollout:

```bash
kubectl rollout pause deployment/fortune-app-blue
```

Verify status:

```bash
kubectl rollout status deployment/fortune-app-blue
```

Resume the rollout:

```bash
kubectl rollout resume deployment/fortune-app-blue
kubectl rollout status deployment/fortune-app-blue
```

### Rollback

If you detect a bug, rollback to the previous revision:

```bash
# Roll back to the previous revision
kubectl rollout undo deployment/fortune-app-blue

# Or roll back to a specific revision number
kubectl rollout undo deployment/fortune-app-blue --to-revision=1
```

Verify the rollback completed:

```bash
kubectl rollout status deployment/fortune-app-blue
kubectl get pods -l app=fortune-app -o wide
```

---

## Canary deployments

Canary deployments run a small number of pods with the new version alongside the current version so you can test before shifting all traffic.

1. Create the canary deployment manifest:

```bash
kubectl apply -f deployments/fortune-app-canary.yaml
```

2. Verify pods and use the service to test the canary. You can adjust traffic by scaling the canary up and the blue down:

```bash
kubectl get deployments
kubectl get pods -l app=fortune-app

# Example: scale canary to 1 and blue to 4
kubectl scale deployment fortune-app-canary --replicas=1
kubectl scale deployment fortune-app-blue --replicas=4
```

3. If tests pass, increase canary replicas or update the blue deployment image and roll it out.

If canary fails, delete the canary deployment and keep the stable version:

```bash
kubectl delete -f deployments/fortune-app-canary.yaml
```

---

## Blue–Green deployments

Blue–green keeps two separate, fully functional environments (blue = current, green = new). Switch traffic by repointing the Service.

1. Apply the blue service (if not already applied):

```bash
kubectl apply -f services/fortune-app-blue-service.yaml
```

2. Create the green deployment for version 2.0.0:

```bash
kubectl apply -f deployments/fortune-app-green.yaml
kubectl get deployments -l app=fortune-app
```

3. Point the Service to the green deployment (apply updated service manifest):

```bash
kubectl apply -f services/fortune-app-green-service.yaml
kubectl get svc fortune-app -o yaml
```

4. Verify traffic is served by the green pods (check pod IPs, logs, or version output):

```bash
kubectl get pods -l version=green -o wide
# or check logs
kubectl logs -l version=green
```

### Blue–Green rollback

To roll back, re-apply the service manifest for the blue deployment:

```bash
kubectl apply -f services/fortune-app-blue-service.yaml
```

Verify the service is again routing to the blue pods and that traffic is healthy.

---

## Helpful verification & troubleshooting commands

```bash
kubectl get deployments
kubectl get replicasets
kubectl get pods -o wide
kubectl describe deployment fortune-app-blue
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

## Cleanup

When you’re done with the lab, delete resources to avoid charges:

```bash
kubectl delete -f services/fortune-app-green-service.yaml || true
kubectl delete -f services/fortune-app-blue-service.yaml || true
kubectl delete -f services/fortune-app.yaml || true
kubectl delete -f deployments/fortune-app-canary.yaml || true
kubectl delete -f deployments/fortune-app-green.yaml || true
kubectl delete -f deployments/fortune-app-blue.yaml || true

# Delete the GKE cluster (this will remove all cluster resources)
gcloud container clusters delete bootcamp --quiet
```

---

If you want, I can also:
- Convert this into a README-style file in the repository (I can commit it for you). 
- Create example manifests (canary/green) if they are missing.

