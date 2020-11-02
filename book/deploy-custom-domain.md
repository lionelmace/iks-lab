# Using Custom URLs for your app by adding Alternate Domain Names (CNAMEs)

In Cloud Internet Services, an alternate domain name, also known as a CNAME, lets you use your own domain name (for example, www.example.com) in your app’ URL instead of using the domain name that IKS assigns to your cluster.

In this section, we will leverage two IBM Cloud Services:
* [CIS - Cloud Internet Services](https://cloud.ibm.com/catalog/services/internet-services) which includes Domain Name Service (DNS), Global Load Balancer (GLB), Distributed Denial of Service (DDoS) protection, Web Application Firewall (WAF), etc.

* [Certificate Manager](https://cloud.ibm.com/catalog/services/certificate-manager) to order and manage SSL/TLS certificates for your app.

## Create a custom domain with CIS

1. You will need an Internet Services instance. You can create one for free [CIS - Cloud Internet Services](https://cloud.ibm.com/catalog/services/internet-services)

1. Get the hostname of your VPC Load Balancer created automatically for your cluster


1. To create a new domain, go to Reliability > DNS

    ```
    CNAME mytodo.example.com > URL fo
    ```

## Create a TLS certificate for the new domain

1. Create a free instance of [Certificate Manager](https://cloud.ibm.com/catalog/services/certificate-manager)

1. Vérifier les autorisations IAM pour autoriser l’accès de Certificate Manager à Cloud Internet Services
https://cloud.ibm.com/docs/certificate-manager?topic=certificate-manager-ordering-certificates#cis

1. Aller sur l’instance Certificate Manager du cluster

1. Order Certificate, select Cloud Internet Services. Select sub-domain.

1. Get your new certificate CRN


## Create a new secret in your cluster to store 

1. Get your cluster id

    ```sh
    ibmcloud ks cluster get -c <cluster-name>
    ```

1. Create a new secret
    ```
    ibmcloud ks ingress secret create --name <secret-name> --cluster <cluster-id> --cert-crn <cert-crn>
    ```
kubectl get secrets -n ibm-cert-store (edited)

## Re-deploy your app with the new domain

1. Edit your yaml

    ```yaml
    ---
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: mytodo-ingress
      namespace: mytodo
      annotations:
        # Force the use of https if the request is http
        ingress.bluemix.net/redirect-to-https: "True"
    spec:
      tls:
      - hosts:
        - <your-custom-domain>
        secretName: <new-cert-secret>
      rules:
      - host: <your-custom-domain>
        http:
          paths:
          - path: /
            backend:
              serviceName: mytodo
              servicePort: 8080
    ```

1. Deploy

    ```sh
    kubectl apply -f mytodo.yaml
    ```