# Solution to Application Issues
## Issue 1:The Pod FailedScheduling  i.e "pod/redis-cart-7cf4567bb6-kh9rq" 
 Error 1 :Warning  FailedScheduling  59s (x7 over 6m9s)  default-scheduler  0/2 nodes are available: 1 node(s) didn't match Pod's node affinity, 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate.
 - 0/2 nodes are available: 1 node(s) didn't match Pod's node affinity, 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, indicates that the pod is not able to be scheduled on the available nodes due to two main reasons :
 - Node Affinity: The pod has a nodeSelector which restricts it to only be scheduled on nodes with the label kubernetes.io/hostname: test-worker-2. However, it seems that either the node test-worker-2 is unavailable or does not exist.
- Taints: The node has a taint (node-role.kubernetes.io/master) that prevents the pod from being scheduled on master nodes unless the pod tolerates the taint.
#######Solutions#########
**Check if the Node Exists** : Ensure that a node labeled kubernetes.io/hostname: test-worker-2 exists. If this node doesn't exist, either create the node with this label or update the nodeSelector to match an available node's label.
**Add Toleration for Master Node**: If your cluster's master node is being used for workloads, you'll need to add a toleration to allow the pod to be scheduled on that node, as the taint node-role.kubernetes.io/master is preventing the pod from being scheduled on master nodes.
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



