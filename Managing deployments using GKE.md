Managing Deployments Using Kubernetes Engine


===================================================
1.Get sample code for this lab

gcloud storage cp -r gs://spls/gsp053/kubernetes .
cd kubernetes

2.Create a cluster with 3 nodes

gcloud container clusters create bootcamp \
  --machine-type e2-small \
  --num-nodes 3 \
  --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"
  
3.Create a deployment
kubectl create -f deployments/fortune-app-blue.yaml

4.create a service to expose the fortune-app deployment externally.
kubectl create -f services/fortune-app.yaml

----------------------------------------------
5.Scale a deployment
kubectl scale deployment fortune-app-blue --replicas=5

6.Now scale back the application:
kubectl scale deployment fortune-app-blue --replicas=3

Rolling update

To trigger a rolling update, you can simply apply the configuration of the "green" deployment. Kubernetes is smart enough to see the existing deployment (fortune-app-blue) and will "roll" the changes from the new file onto it.
kubectl edit deployment fortune-app-blue
Copied!
In the editor, find the image line and change the version tag from 1.0.0 to 2.0.0. You can edit the file by pressing i on your keyboard to enter "insert mode".
First, change the image tag:

Find this line: image: "us-central1-docker.pkg.dev/qwiklabs-resources/spl-lab-apps/fortune-service:1.0.0"
And change it to: image: "us-central1-docker.pkg.dev/qwiklabs-resources/spl-lab-apps/fortune-service:2.0.0"
Next, update the environment variable:

Find the env section and the APP_VERSION variable.
Change value: "1.0.0" to value: "2.0.0"
Save and close the editor. You can do this by pressing Esc, then typing :wq, and pressing Enter. This will trigger the rolling update on the correct deployment and properly record its history. This will trigger the rolling update on the correct deployment and properly record its history.

Pause a rolling update
kubectl rollout pause deployment/fortune-app-blue

Verify the current state of the rollout:
kubectl rollout status deployment/fortune-app-blue


Resume a rolling update

kubectl rollout resume deployment/fortune-app-blue

When the rollout is complete, you should see the following when running the status command:
kubectl rollout status deployment/fortune-app-blue



Roll back an update
Assume that a bug was detected in your new version.

Use the rollout command to roll back to the previous version:
kubectl rollout undo deployment/fortune-app-blue


Canary deployments
First, create a new canary deployment for the new version using the fortune-app-canary.yaml file:
kubectl create -f deployments/fortune-app-canary.yaml


Blue-green deployments
kubectl apply -f services/fortune-app-blue-service.yaml

Updating using Blue-Green deployment
Now, create the new "green" deployment for version 2.0.0.
kubectl create -f deployments/fortune-app-green.yaml

Now, update the service to point to the new "green" version:
kubectl apply -f services/fortune-app-green-service.yaml


Blue-Green rollback
To roll back, simply re-apply the service manifest for the "blue" deployment:
kubectl apply -f services/fortune-app-blue-service.yaml