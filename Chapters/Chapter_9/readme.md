CHAPTER – 9 : SECURITY


       **KUBECONFIG TOPIC **
To change current context we use 
-Kubectl config use-context <contextname>  --kubeconfig  /root/my-kube-config
-	Kubectl is using default kube config file that is “kubeconfig” so we have to change the file bcoz we are not using that default file we are using my-kube-config , that’s y we run this command “--kubeconfig  /root/my-kube-config”
-Default config file is located in the /root/.kube/config

Q:13 is undone .

RBAC (Role Base Access Control)
-	K config view -> use to see the config file 
Role : 
-k create -f developer-role.yaml
-kubectl get roles 
-kubectl describe role developer

RoleBinding: 
-k create -f devuser-developer-binding.yaml
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
--------------------------------------------------------------------------------------------------

ADMISSION CONTROLLERS

what does -it flag and exec command do ?

## `kubectl exec`

**`kubectl exec`** lets you run a command inside a running container in a pod — similar to how `docker exec` works for Docker containers. It's used for debugging, inspecting files, running diagnostic tools, or getting a shell into a container.

## `-it` flags

The `-it` flag is actually two separate flags combined:

- **`-i` (--stdin)** — Keeps the session's standard input open, so you can type commands interactively. Without it, stdin is closed and the session can't accept your keyboard input.

- **`-t` (--tty)** — Allocates a pseudo-TTY (terminal), which gives you a proper terminal interface. Without it, you'd get raw output without shell features like prompt display, command history, or line editing.

**Together**, `-it` gives you an interactive terminal session — you type commands and see live output, just like a regular SSH session into a remote machine.

## Full breakdown

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

| Part | What it does |
|------|-------------|
| `kubectl exec` | Execute a command in a pod's container |
| `-i` | Keep stdin open (allow input) |
| `-t` | Allocate a terminal (proper shell UX) |
| `<pod-name>` | Target pod |
| `--` | Separator between `kubectl` flags and the command |
| `/bin/bash` | The command to run (start a bash shell) |

Without `-it`, running `kubectl exec <pod> -- ls` works fine for one-off commands (it prints output and exits), but you couldn't get an interactive shell experience.

Q:To check which admisiion controller is enabled by default ?
A:kubectl exec -it kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep 'enable-admission-plugins'

Q3:which admission controller is enabled in this cluster which is normally disabled?
A: vi/etc/kubernetes/manifests/kube-apiserver.yaml  
- Then look enable-admission-plugins in spec section 
- If you dont want to get in the vi (yaml file) write this command 
   - grep enable-admission-plugins / etc/kubernetes/manifests/kube-apiserver.yaml
          - this is the path -> /etc/kubernetes/manifests/kube-apiserver.yaml

Q4: you have to disable NamespaceExits admission controller and enable NamespaceAutoProvision.
A:"NamespaceExits" admission controller restrict to create a new pod in the non existing namespace but "NamespaceAutoProvision" make it on the spot when u run the command . So do that we have to go inthe yaml file of the pod
    - vi /etc/kubernetes/manifests/kube-apiserver.yaml
      -Go to the spec->containers->command->enable-admission-plugins   add their NameSpaceAutoProvision    then wait for few seconds validations should finish and restart the process .


Q5: Create the pod using nginx image in the blue namespace :
A: After enabling NamespaceAutoProvision the blue namespace auto created and pod will create in that namesapce .
     -kubectl run nginx --image nginx -n blue 


--NOTE **NamespaceExits** and **NamespaceAutoProvision** is deprecated now **NamespaceLifecycle** admission controller will make sure that requests to a non-existent namespace is rejected and that the default namespaces such as default 'kube-sysytem' and 'kube-public' cannot be deleted     

Q8: Disabled the **DefaultStorageClass** admission controller because the user doesnot want a storage class :
A: Go to the 
    -vi /etc/kubernetes/manifests/kube-apiserver.yaml
        -Then spec->conatiners>commands
           - Add their      --disable-admission-plugins=DefaultStorageClass


Q9: Since the kube-apiserver is running as pod you can check the process to see enabled and disabled plugins.
A:  ps -ef | grep kube-apiserver | grep admission-plugins
        -This will list out all enable and disable plugins 

NOTES:
- what does ps do in kubernetes?
`ps` is not a Kubernetes-specific command — it's a standard Linux command that lists running processes. When used **inside a Kubernetes pod**, it shows the processes running within that container.

For example:

```bash
kubectl exec <pod-name> -- ps aux
```

This would show the processes inside that specific container (which often includes just the main application process and maybe a shell).

If you meant something else — like `pods` in Kubernetes, those are the smallest deployable units that run your containers.



- What does -ef do in kubernetes?    
`-ef` is not a Kubernetes command — it's a flag for the standard Linux `ps` command.

`ps` stands for "process status," and the flags work like this:

- **`-e`** — Select all processes (every process on the system)
- **`-f`** — Full-format listing (shows more details like UID, PPID, C, STIME, etc.)

So `ps -ef` together means: **"Show all processes in full detail."**

You'd typically run it inside a pod to inspect what's running:

```bash
kubectl exec <pod-name> -- ps -ef
```

This outputs something like:

```
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 10:15 ?        00:00:00 /bin/myapp
root        10     0  0 10:16 ?        00:00:00 sleep 3600
```

If you were thinking of something else like `kubectl -ef` — that doesn't exist. You might be thinking of `kubectl -f` (follow logs) or `kubectl -e` (environment variables), but those are different things.

--------------------------------------------------------------------------------------------------------------------------
MUTATING AND VALIDATING ADMISSION CONTROLLER 

There are two types of Admission Controller:
1- MUTATING ADMISSION CONTROLLER
2- VALIDATING ADMISSION CONTROLLER


- ## Mutating Admission Controllers:
       When the 'DefaultStorageClass' admission controller is enabled it will automatically create the strage and assign to the pod still you didn't mentioned in ur .yaml file ,it will auromatically create and assign . This is called 'Mutating Admission Controller' 

These controllers can modify (mutate) objects as they pass through:

1. **DefaultStorageClass** — Sets default storage class for PVCs without one
2. **DefaultTolerationSeconds** — Sets default toleration seconds for taints
3. **MutatingAdmissionWebhook** — Calls external webhooks that can mutate objects
4. **NamespaceLifecycle** — Ensures namespaces exist and aren't terminating (also validates)
5. **PodNodeSelector** — Applies namespace-level node selector constraints to pods
6. **PodPriority** — Sets priority class based on priority value
7. **PodTolerationRestriction** — Applies namespace-level tolerations to pods
8. **AlwaysPullImages** — Modifies pods to set `imagePullPolicy: Always`
9. **ImagePolicyWebhook** — Can modify image-related policies via webhook
10. **SecurityContextDeny** — (deprecated) Was used to deny pods with certain security contexts




## Validating Admission Controllers

These controllers validate (reject/allow) but do not modify objects:

1. **AlwaysAdmit** — Admits all requests (essentially a pass-through)
2. **AlwaysDeny** — Denies all requests (used only for testing)
3. **CertificateApproval** — Validates CertificateSigningRequest approvals
4. **CertificateSigning** — Validates CSR objects
5. **ClusterTrustBundleAttest** — Validates ClusterTrustBundle attestation
6. **DenyServiceExternalIPs** — Rejects Services with externalIPs
7. **EventRateLimit** — Rate-limits API requests
8. **ExtendedResourceToleration** — Validates extended resource tolerations
9. **ImagePolicyWebhook** — Validates images against a backend policy
10. **LimitPodHardAntiAffinityTopology** — Validates anti-affinity topology keys
11. **LimitRanger** — Validates pods don't exceed namespace limits
12. **NodeRestriction** — Limits what nodes can modify via kubelet
13. **OwnerReferencesPermissionEnforcement** — Validates owner reference permissions
14. **PersistentVolumeClaimResize** — Validates PVC resize requests
15. **PersistentVolumeClaimValidate** — Validates PVCs
16. **PodSecurity** — Enforces Pod Security Standards (replaces PSP)
17. **Priority** — Validates priority class existence
18. **ResourceQuota** — Validates resource quota compliance
19. **ServiceAccount** — Validates service account references
20. **TaintNodesByCondition** — (can run as validating)
21. **ValidatingAdmissionPolicy** — Validates using CEL expressions (born on feature)
22. **ValidatingAdmissionWebhook** — Calls external webhooks for validation



## Controllers That Do Both (Mutating + Validating) the request

A few controllers operate as **both** mutating and validating:

1. **NamespaceLifecycle** — Mutates to ensure namespace creation follows rules and validates that namespaces aren't terminating
2. **PodSecurity** — Can be configured in different modes (enforce/audit/warn); the enforce mode validates, while warn/audit don't mutate but provide feedback
3. **ImagePolicyWebhook** — Can act as both: validates images against external policy and can mutate image-related fields
4. **LimitRanger** — Primarily validates, but in some configurations sets default limits (though this is often handled by its separate defaulting logic)
5. **MutatingAdmissionWebhook + ValidatingAdmissionWebhook** — These are separate controllers, but the **same webhook server** behind them can handle both mutation and validation if registered for both phases

- in both first mutating admission controller runs then validating 

> **Note:** Most admission controllers specialize in one role. The pattern for truly dual-mode behavior is using **admission webhooks** — where a single webhook server registers both a `MutatingWebhookConfiguration` and a `ValidatingWebhookConfiguration`, allowing it to mutate in one phase and validate in the next.


 -------These all above are default admission controllers what if we want our own admission controller that has muatations and validations our own logic --------------

- To support our customize admssion controllers there are two special admission controllers available **Mutating Admission Webhook ** and **Validating Admission Webhook**
- you can host them in the kubernetes and outside 
- After request go through all the built-in admission controllers it hits the webhook thats configured
- When it hits webhook ser it makes the call to the admission webhook server like passing in admission review object      in json format
-First we deploy/host the admission webhook then we make admission webhook configuration object .
-You can make the server in any language you want like go,python
-you can deploy webhook admission in any server or make it or deloy it in the kubernetes(make image and containerized it) ,if you deploy on kubernetes so you have to make **service** of it also 
- Next step is to configure our cluster to reach out the service to validate and mutate the request ,for this we make 'webhook configuration object'

- Communication between 'webhook server ' and 'apiserver' has to be over TLS so certificate bundle should be configured so ther server has configured with the pair of certificte,then certificate has to be made and pass to the client certificate ssca bundle
- (Communication between the webhook server and the API server must be over TLS, so a certificate bundle should be configured. The server needs a pair of certificates (a certificate and its corresponding key), and the client's CA certificate bundle must include the CA that signed the server's certificate.) all these mentioned in the clientConfig section


- Now u have to tell api when to call webhook for validation , so we  want to call webhook for only when creating, deleting,getting the pod so u have to mention it in the rules section,,, final conclusion **everytime when we create the pod it will call the webhook to vaidate** .

- What is TLS in kubernetes ? 

TLS (Transport Layer Security) in Kubernetes is used to secure communication between various components of the cluster. There are several key areas where TLS applies:

**1. API Server Communication**
- The API server exposes an HTTPS endpoint that all clients (kubectl, pods, other components) connect to over TLS
- It presents a server certificate signed by the cluster's CA

**2. Component-to-Component TLS**
- **etcd**: API server connects to etcd over TLS
- **kubelet**: API server talks to kubelets on worker nodes over TLS
- **kube-scheduler, kube-controller-manager**: These use TLS certificates to authenticate to the API server

**3. Certificate Types**
- **CA (Certificate Authority)**: A root certificate that signs all other certificates in the cluster. Typically created during cluster initialization (e.g., `kubeadm init` generates it)
- **Server certificates**: Presented by servers to prove their identity (e.g., API server cert)
- **Client certificates**: Used by clients to authenticate themselves (e.g., kubectl's client cert)
- **kubeconfig files**: Bundle the client certificate, key, and CA cert so tools like kubectl can authenticate

**4. In-cluster Service TLS**
- Pods and services can also use TLS for their own communication (e.g., a webhook server you mentioned)
- Tools like cert-manager help automate certificate management for these use cases

In your original sentence's context: the webhook server needs a **server certificate** for clients to verify its identity, and the API server (as the client in that connection) needs the **CA certificate** that signed the webhook server's certificate — this is the "CA bundle" you were referring to.

-Q4 of lab:
Create a TLS secret named webhook-server-tls in the webhook-demo namespace.
This secret will be used by the admission webhook server for secure communication over HTTPS.
We have already created below cert and key for webhook server which should be used to create secret.
Certificate : /root/keys/webhook-server-tls.crt
Key : /root/keys/webhook-server-tls.key

SOL:
- kubectl -n webhook-demo create secret tls webhook-server-tls --cert "certification file name"  --key "key file name"


-Last answer due to this problem 
    -runAsNonRoot: true — This setting tells Kubernetes to ensure the container does NOT run as the root user (user ID 0).
    -runAsUser: 0 — This explicitly sets the user ID to 0, which is the root user.