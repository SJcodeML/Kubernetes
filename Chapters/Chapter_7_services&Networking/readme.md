- All pods are allow to communicate with other pods in cluster through IP addresses and services . 
- Network Policies : It is an object 


![alt text](image.png)

An ingress controller is a specialized proxy and load balancer that runs inside a Kubernetes cluster to manage external (HTTP/HTTPS) traffic. We need them to provide a single public entry point, enable smart path- and host-based routing, and handle SSL/TLS termination without paying for multiple cloud load balancers.What is an Ingress Controller?Acts as a smart reverse proxy sitting at the edge of a cluster.Reads and implements the routing rules defined in Kubernetes Ingress resource objects.Translates API rules into live configurations for engines like NGINX, Envoy, or Traefik.Why Do We Need Them?Single Entry Point: Instead of making a separate costly cloud load balancer for every single microservice, all traffic goes through one primary address.Host and Path Routing: Directs traffic based on URL paths (e.g., /shop vs /blog) or domain names (e.g., shop.com vs blog.com).TLS/SSL Termination: Secures web traffic by handling HTTPS certificates centrally at the cluster boundary.Traffic Management: Supports advanced features like load balancing, health checks, and canary traffic splitting.For a quick visual breakdown of what an ingress controller is and how it functions:

------------
What is SSL and TSL ?

SSL (Secure Sockets Layer) and TLS (Transport Layer Security) are cryptographic protocols that encrypt data sent over the internet to prevent hackers from reading or modifying it. TLS is the modern, secure successor to SSL, though people often use the term "SSL" to refer to both.

The Core DifferenceSSL: Created by Netscape in the 1990s. It is completely obsolete and insecure.TLS: The modern upgrade to SSL. It is highly secure and actively used today.The Name: The industry still uses the term "SSL" or "SSL/TLS" out of habit.

Why We Need ThemEncryption: Scrambles data sent between a browser and a server (e.g., credit card numbers, passwords) so attackers cannot intercept it.Authentication: Verifies that the website you are connecting to is legitimate and not a fake site.Data Integrity: Ensures that data cannot be altered or corrupted during transit without being detected.Browser Trust: Enables the https:// prefix and the padlock icon in the browser address bar