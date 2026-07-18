CHAPTER – 9 : SECURITY
KUBECONFIG TOPIC 
To change current context we use 
-Kubectl config use-context <contextname>  --kubeconfig  /root/my-kube-config
-	Kubectl is using default kube config file that is “kubeconfig” so we have to change the file bcoz we are not using that default file we are using my-kube-config , that’s y we run this command “--kubeconfig  /root/my-kube-config”
-Default config file is located in the /root/.kube/config
Q:13 is undone .
RBAC (Role Base Access Control)
-	K config view -> use to see the config file 
Role : k create -f developer-role.yaml
-kubectl get roles 
-kubectl describe role developer
RoleBinding: k create -f devuser-developer-binding.yaml
-kubectl get rolebindings
-kubectl describe rolebindings devuser-developer-binding
CHECKING ACCESS WHICH USER HAS WHAT PERMISSIONS ;
-Kubectl auth can-i create deployments
 -kubectl auth can-i delete nodes
-kubectl auth can-i create deployments –as dev-user
-kubectl auth can-i create pods –as dev-user
-kubectl auth can-i create pods –as dev-user –namespace test
RESOURCE NAMES:
-If we have to give access to a certain developer to engage in specific pods so we define 
resourceNames in rules section to give access to certain pods like :
rules :
   resourceNames:[“blue” , “orange”]
-all authorization information in this file 
/etc/kubernetes/manifests/kube-apiserver.yaml
-it will grep all authorization information from the file kube-apiserver.yaml
   ps -aux  | grep authorization
Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace.Use the given spec:
-	Role: developer
-	Role Resources: pods
-	Role Actions: list
-	Role Actions: create
-	Role Actions: delete
-	RoleBinding: dev-user-binding
-	RoleBinding: Bound to dev-user
K create role --help
K create role developer –verb=list,create,delete –resource=pods
K create rolebinding dev-user-binding –role=developer –user=dev-user

A set of new roles and role-bindings are created in the blue namespace for the dev-user. However, the dev-user is unable to get details of the dark-blue-app pod in the blue namespace. Investigate and fix the issue.We have created the required roles and rolebindings, but something seems to be wrong.
 ------------------------------------------------------------------------------------------------------------------------------------------
Cluster Roles:
-NameSpaced: pods, jobs, services, roles, configmaps,replicasets, deployments, secrets, rolebindings, PVC
-CLusterSpaces: nodes, PV, clusterroles, clusterrolebindings, certificatesigningrequests, namespaces
-To get cluster roles :
Kubectle get clusterroles –no-headers | wc –l
-To get cluster role bindings 
Kubectl get clusterrolebindings –no-headers | wc –l

Kubectl get clusterrolebindings | grep cluster-admin
 
-Get specific clusterrole 
k get clusterrolebindings | grep cluster-admin 

Question 7: A new user michelle joined the team . She will be focusing on the nodes in the cluster . Create the required ClusterRoles and ClusterRoleBindings so she gets access to the nodes . 

Solution:
-k create clustertole --help
-k create clusterrolebinding --help

-we create ckuster role 
kubectl create clusterrole michelle-role --verb=get,list,watch --resourc
e=nodes

-we bind the cluster role to the user that's y we are creating clusterrolebinding 
kubectl create clusterrolebinding michelle-role-binding --clusterrole=michelle-role --user=michelle

-To check 
kubectl describe clusterrole michelle-role
kubectl describe clusterrolebinding michelle-role-binding

k get nodes --as michelle

QUESTION:8 SOLUTION : 

-k create clusterrole storage-admin --resource=persistentvloumes,storageclasses --verb=list,create,get,watch
-To check -> 
- k describe clusterrole storage-admin  
- k get clusterrole storage-admin -o yaml

-To check either michelle get the access of storage classes or not 

k --as michelle get storageclass


