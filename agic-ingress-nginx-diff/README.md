## Application Gateway Ingress Controller (AGIC):

### Pros:

- Azure native integration: AGIC is an Azure-native solution, which offers seamless integration with other Azure services, such as Azure Front Door, Azure Web Application Firewall, and Azure Private Link.
- Security: Application Gateway offers built-in Web Application Firewall (WAF) to protect your applications from common web vulnerabilities and exploits.
- SSL termination: AGIC supports SSL termination, allowing you to offload SSL decryption from your application instances, improving their performance.
- Multi-site hosting: You can use a single Application Gateway instance to manage traffic for multiple applications or domains.

### Cons:

- Cost: Application Gateway can be more expensive compared to other Ingress Controllers, as it's a managed service with additional features like WAF.
- Limited customizability: Being a managed service, you may have less control and customizability compared to open-source Ingress Controllers like ingress-nginx.


## ingress-nginx Ingress Controller:

### Pros:

- Open-source and community-driven: ingress-nginx is an open-source and community-driven project, which means it has extensive support, a wide range of features, and frequent updates.
- Flexibility: ingress-nginx offers more flexibility in terms of configuration options and customization to cater to specific needs.
- Cost-effective: As an open-source solution, it can be more cost-effective than managed services like Application Gateway, especially for smaller deployments.

### Cons:

- Increased maintenance overhead: Since ingress-nginx is not a managed service, you'll need to manage, scale, and update the Ingress Controller yourself, which might increase maintenance overhead.
- Less integrated with Azure ecosystem: While it can be used with AKS, ingress-nginx may not have the same level of integration with other Azure services as AGIC.


### Advanced load balancing algorithms: 
Nginx Plus provides a variety of advanced load balancing algorithms, such as Least Time, Least Connections, and IP Hash, that can offer better performance and control over traffic distribution compared to AGIC.

### Active health checks: 
Nginx Plus can perform active health checks on backend services, enabling faster detection and recovery from issues. It can also proactively remove unhealthy backend services from the load balancing pool, ensuring better availability and performance.

### Session persistence: 
Nginx Plus supports session persistence, which maintains user sessions across multiple requests by directing traffic to the same backend server. This feature can be beneficial for applications that require stateful connections.

### Dynamic reconfiguration: 
Nginx Plus allows for dynamic reconfiguration of upstream servers without the need to reload the configuration, reducing potential downtime and ensuring seamless updates.

### Caching: 
Nginx Plus offers advanced caching capabilities, such as content caching, cache purging, and cache revalidation. These features can help improve the performance of your applications by reducing the load on backend services and minimizing response times.

### Advanced metrics and monitoring: 
Nginx Plus provides advanced metrics, monitoring, and logging features, allowing you to gain better insights into your applications' performance and troubleshoot issues more effectively.

```yaml
service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /hello-world-one(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port:
              number: 80
      - path: /hello-world-two(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-two
            port:
              number: 80
      - path: /(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port:
              number: 80
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /static(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port: 
              number: 80
```