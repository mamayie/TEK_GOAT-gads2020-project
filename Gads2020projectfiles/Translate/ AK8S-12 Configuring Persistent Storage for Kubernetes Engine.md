
# LAB: AK8S-12 Configuring Persistent Storage for Kubernetes Engine\

## Objectives: In this lab, you learn how to perform the following tasks:\

Create manifests for PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs) for Google Cloud Platform (GCP) persistent disks (dynamically created or existing)

Mount GCP persistent disk PVCs as volumes in Pods

Use manifests to create StatefulSets

Mount GCP persistent disk PVCs as volumes in StatefulSets

Verify the connection of Pods in StatefulSets to particular PVs as the Pods are stopped and restarted

## Steps:

1. Access Qwiklabs.
* gcloud alpha cloud-shell OR
gcloud cloud-shell activate OR
gcloud init


Task 1. Create PVs and PVCs

* Create a GKE cluster

1. Create environment variables for the GCP zone and cluster name that will be used to create the cluster for this lab.

export my_zone=us-central1-a
export my_cluster=standard-cluster-1

2. Configure tab completion for the kubectl command-line tool.

source <(kubectl completion bash)

3. Create a VPC-native Kubernetes cluster.

gcloud container clusters create $my_cluster
   --num-nodes 3 --enable-ip-alias --zone $my_zone

4. Configure access to your cluster for kubectl:

gcloud container clusters get-credentials $my_cluster --zone $my_zone

* Create and apply a manifest with a PVC.
1. Clone the repository to the lab Cloud Shell.

 .git clone https://github.com/GoogleCloudPlatformTraining/training-data-analyst

2. Change to the directory that contains the sample files.

.cd ~/training-data-analyst/courses/ak8s/12_Storage/

3. Show that you currently have no PVCs:

.kubectl get persistentvolumeclaim\

4. Create the PVC.

. kubectl apply -f pvc-demo.yaml

5. Show newly created PVC

. kubectl get persistentvolumeclaim\

Task 2.  Mount and verify GCP persistent disk PVCs in Pods

* Mount the PVC to a Pod.

1. Create the Pod with the volume.

. kubectl apply -f pod-volume-demo.yaml

2. List the Pods in the cluster.

.kubectl get pods

3. verify the PVC is accessible within the Pod.

. kubectl exec -it pvc-demo-pod -- sh\

4. Create a simple text message as a web page in the Pod.

. echo Test webpage in a persistent volume!>/var/www/html/index.html\
chmod +x /var/www/html/index.html

5. Verify the text file contains your message.

. cat /var/www/html/index.html\

6. eave the interactive shell on the nginx container.

exit + enter

* Test the persistence of the PV

1. Delete the pvc-demo-pod.

.kubectl delete pod pvc-demo-pod

2. List the Pods in the cluster.

.kubectl get pods

3. Show PVC.

.kubectl get persistentvolumeclaim\

4. Redeploy the pvc-demo-pod.

. kubectl apply -f pod-volume-demo.yaml

5. List the Pods in the cluster.

. kubectl get pods

6. verify the PVC is is still accessible within the Pod.

. kubectl exec -it pvc-demo-pod -- sh

7.  verify that the text file still contains your message .

. cat /var/www/html/index.html

8. leave the interactive shell on the nginx container.

exit + enter

9. Task 3. Create StatefulSets with PVCs.
* Release the PVC.

1. Before you can use the PVC with the statefulset, you must delete the Pod that is currently using it. Execute the following command to delete the Pod:

. kubectl delete pod pvc-demo-pod

2. Confirm the Pod has been removed.

. kubectl get pods

* Create a StatefulSet.
. kubectl apply -f statefulset-demo.yaml


* Verify the connection of Pods in StatefulSets.

1. Use "kubectl describe" to view the details of the StatefulSet.

. kubectl describe statefulset statefulset-demo

2. List the Pods in the cluster.

. kubectl get pods


3. list the PVCs.

. kubectl get pvc

4. Use "kubectl describe" to view the details of the first PVC in the StatefulSet:

. kubectl describe pvc hello-web-disk-statefulset-demo-0

Task 4. Verify the persistence of Persistent Volume connections to Pods managed by StatefulSets

1.  verify that the PVC is accessible within the Pod, you must gain shell access to your Pod. To start the shell session

. kubectl exec -it statefulset-demo-0 -- sh

2. Verify that there is no index.html text file in the /var/www/html directory.

. cat /var/www/html/index.html

3. Create a simple text message as a web page in the Pod

. echo Test webpage in a persistent volume!>/var/www/html/index.html\
chmod +x /var/www/html/index.html

4. Verify the text file contains your message.

. cat /var/www/html/index.html

5. leave the interactive shell on the nginx container.
 . exit

6. Delete the Pod where you updated the file on the PVC.

. kubectl delete pod statefulset-demo-0\

7. List the Pods in the cluster.

. kubectl get pods


8. Connect to the shell on the new statefulset-demo-0 Pod.

.kubectl exec -it statefulset-demo-0 -- sh

9. Verify that the text file still contains your message.

. cat /var/www/html/index.html

10. eave the interactive shell on the nginx container.

.exit + enter 
