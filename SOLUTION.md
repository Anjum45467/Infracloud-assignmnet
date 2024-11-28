# Solution to Application Issues
## Issue 1:The Pod FailedScheduling  i.e "pod/redis-cart-7cf4567bb6-kh9rq" 
 Error 1 : "Warning  FailedScheduling  59s (x7 over 6m9s)  default-scheduler  0/2 nodes are available: 1 node(s) didn't match Pod's node affinity, 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate".
 - 0/2 nodes are available: 1 node(s) didn't match Pod's node affinity, 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, indicates that the pod is not able to be scheduled on the available nodes due to two main reasons :
 - Node Affinity: The pod has a nodeSelector which restricts it to only be scheduled on nodes with the label kubernetes.io/hostname: test-worker-2. However, it seems that either the node test-worker-2 is unavailable or does not exist.
- Taints: The node has a taint (node-role.kubernetes.io/master) that prevents the pod from being scheduled on master nodes unless the pod tolerates the taint.

***#######Solutions#########***

- Check if the Node Exists* : Ensure that a node labeled kubernetes.io/hostname: test-worker-2 exists. If this node doesn't exist, either create the node with this label or update the nodeSelector to match an available node's label.

- Add Toleration for Master Node*: If your cluster's master node is being used for workloads, you'll need to add a toleration to allow the pod to be scheduled on that node, as the taint node-role.kubernetes.io/master is preventing the pod from being scheduled on master nodes.
spec:
      # If you want to tolerate taints or remove nodeSelector
      # nodeSelector:
      #   kubernetes.io/hostname: test-worker-2
      
      tolerations:
        - key: "node-role.kubernetes.io/master"
          operator: "Exists"
          effect: "NoSchedule"
**Explanation of changes:**

- Toleration for Master Node: This line allows the pod to tolerate the taint on master nodes (node-role.kubernetes.io/master: NoSchedule). If your master node is used for running workloads, this will let the pod be scheduled on it.
- Node Selector: If you want the pod to run only on specific nodes, you can leave or update the nodeSelector field to ensure it matches the correct node labels. Alternatively, you can remove the nodeSelector entirely to allow Kubernetes to choose any available node.
  
## Issue 2:"The deploymnet "recommendationservice" , it was failing to create pods because of a permissions issue related to the service account boutique service account didn’t have the correct permissions to interact with the Kubernetes resources." 

 **Root Cause:**
 
1)Kubernetes uses Service Accounts to manage access to cluster resources. A service account allows pods to interact with the Kubernetes API and access other resources securely.

2)In your case, the recommendationservice deployment specified the boutique service account, but that service account didn’t have the required permissions to operate correctly.
 
3)The error message
  Events:
  Type     Reason        Age                    From                   Message
  ----     ------        ----                   ----                   -------
  Warning  FailedCreate  6m33s (x25 over 128m)  replicaset-controller  Error creating: pods "recommendationservice-86846965dd-" is forbidden: error looking up service account 
  default/boutique: serviceaccount "boutique" not found
  you were seeing indicated that Kubernetes was unable to find the boutique service account, or it existed but didn't have the correct permissions to access the resources it needed.
  
  **Understanding:**
  
1.In the case of the recommendationservice deployment, it was failing to create pods because of a permissions issue related to the service account.

2.The service account boutique was specified in the recommendationservice.yaml file under the spec section like this:
spec
serviceAccountName: boutique
This means that Kubernetes was supposed to use the boutique service account for the pods of this deployment.

3.However, the service account boutique was missing the necessary permissions to allow the deployment to run properly. Specifically, it needed view permissions to interact with other resources in the Kubernetes cluster.

***#######Solutions#########***
- To resolve the issue, we created a role binding using the following command
  --kubectl create rolebinding boutique-rolebinding \
  --clusterrole=view \
  --serviceaccount=default:boutique \
  --namespace=default
- This command binds the view ClusterRole to the boutique service account in the default namespace.
  The view role provides read-only access to resources in the cluster (like services, pods, deployments, etc.), which is what the recommendationservice needed in order to run properly
- By creating the rolebinding, gave the boutique service account the necessary permissions which allowed the deployment to proceed successfully.

