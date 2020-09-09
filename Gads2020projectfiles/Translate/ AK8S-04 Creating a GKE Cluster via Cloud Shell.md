
 # LAB: AK8S-04 Creating a GKE Cluster via Cloud Shell

## Objectives:

In this lab, you learn how to perform the following tasks:

Use kubectl to build and manipulate GKE clusters

Use kubectl and configuration files to deploy Pods

Use Container Registry to store and deploy containers

## Steps:

1. Activate Cloud Shell.

gcloud alpha cloud-shell OR
gcloud cloud-shell activate OR
gcloud init

welcome to Cloud Shell! Type "help"  to get started.
gcpstaging8116_student@qwiklabs-gcp-3f402ffe3ff3cb8:~$

2. Deploy GKE clusters.

export my_zone=us-central1-a
export my_cluster=standard-cluster-1

3. Create a Kubernetes Cluster.

gcloud container clusters create $my_cluster --num-nodes 3 --zone $my_zone --enable-ip-alias


4. Modifying GKE clusters(standard-cluster-1) to have four nodes.

gcloud container clusters resize $my_cluster --zone $my_zone --size=4

5. Using Authication to connect to GKE Cluster.Creating kubeconfig file with credention to allow OAuth2.

gcloud container clusters get-credentials $my_cluster --zone $my_zone

6. Opening kubeconfig file with nano editor.

nano ~/.kube/config

7. Exit editor.

CTRL + X

8. Using kubectl to inspect a GKE cluster.

kubectl config view

9. printing out the cluster.

kubectl cluster-info

10. printing out the active.

kubectl config current-context

11. pritnig details for all the cluster contexts in kubeconfig file.

kubectl config get-contexts

12. Changing active context.

kubectl config use-context gke_$\{GOOGLE_CLOUD_PROJECT\}_us-central1-a_standard-cluster-1

13. viewing rsources usage across nodes cluster.

kubectl top nodes

14.  enabling bash autocompletion for kubectl.

source <(kubectl completion bash)

15. Deploying Pods to GKE clusters(latest version of nginx as a Pod named nginx-1).

kubectl run nginx-1 --image nginx:latest

16. viewing all the deployed Pods in the active context cluster.

kubectl get pods

17. exporting pod, using uniq pod name.

export my_nginx_pod=nginx-1-74c7bbdb84-nvwsc

18. Using echo to confirm environment variable set successfully.

echo $my_nginx_pod

19. viewing pod details.

kubectl describe pod $my_nginx_pod

20. Push a file into a container( opeing test.html file in nano).

nano ~/test.html

21. empty test.html file.

<html> <header><title>This is title</title></header>
<body> Hello world </body>
</html>

22. Exit nano editor.
 CTRL + X + Y

23.  Placing file into nginx container.

 kubectl cp ~/test.html $my_nginx_pod:/usr/share/nginx/html/test.html

 24. create a service to expose our nginx Pod externally.

 kubectl expose pod $my_nginx_pod --port 80 --type LoadBalancer

 25.  view details about services in the cluster.

 kubectl get services

 26. verifing that the copied nginx container is serving the static HTML file.

 curl http://104.154.177.46/test.html

 27. view the resources being used by the nginx Pod.

 kubectl top pods

 28.  Introspect GKE Pods.(cloning repository)
 git clone https://github.com/GoogleCloudPlatformTraining/training-data-analyst\

 29. Changing to directory that contains the sample files.

cd ~/training-data-analyst/courses/ak8s/04_GKE_Shell/

30. Deploying your manifest.

kubectl apply -f ./new-nginx-pod.yaml

31. verifying list of pod.

kubectl get pods

32. Using shell redirection to connect to a Pod\
(starting an interactive bash shell in the nginx container).

kubectl exec -it new-nginx /bin/bash

33. Installing in the nginx bash shell.

apt-get update
apt-get install nano

34. creating a test.html file in the static served directory on the nginx container.

cd /usr/share/nginx/html
nano test.html

35. Creating text.html file in the nginx bash shell nano session.

<html> <header><title>This is title</title></header>
<body> Hello world </body>
</html>

36. exiting the nginx bash shell:
 exit
