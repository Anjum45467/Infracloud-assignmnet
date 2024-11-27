1)Write all the problems and their solutions (the root cause and the change required to solve the issue) in the SOLUTION.md file.
2)Example: Error 1: The "XXX" service pod is going in "ImagePullBackOff" state. Solution: The "XXX" YAML manifest had a typo in image value, after updating the image repository the error disappeared.
Guidelines:
a)This assignment is divided in 3 parts, Part-A, Part-B, and Part-C.
b)For every part, you will have to perform certain task(s). Make sure whenever you make any changes in the YAML manifests to solve any problem, you should commit the changes
c)The SOLUTION.md file also needs to be updated with a small description of the exact problem/error and it's solution. You can also mention the commands that are required to perform any operation while solving any issue.
d)This assignment will require the changes to be made only in the Kubernetes manifests files. There is no need to make any changes in the application code, Docker images.

What next :
Let's deploy the boutique application using the YAML manifests present under deploy/ directory.

kubectl apply -f deploy/
Once you run the above command, you will see that a bunch of Deployments, Services, Pods are getting created on the cluster in the default namespace. You can run the command kubectl get all to check the status of these resources.

Check if all the Deployments are in their desired state.

Make sure all the Pods are in running state. Check if any Pods are in Pending state. If yes, then try to fix them. Once there are no Pods in Pending state, see if all of the Pods enter in ContainerCreating state. If yes then wait for some time. Sit back and relax, it may take up to 20-25 min for all the necessary docker images to get downloaded. So feel free to grab a cup of tea/coffee.

Check if any pod is failing due to any other issue. Now once all the images are downloaded, see if the Pods are in Running state or not. If not then try to find the reason of their failure and fix them. You must see all the 11 service-Pods to be Running.

Once you solve the above issues, make sure you commit the changes in the respective YAML manifest files.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
