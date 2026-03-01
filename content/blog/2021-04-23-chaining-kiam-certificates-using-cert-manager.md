+++
title = "Chaining Kiam Certificates using Cert-manager"
slug = "chaining-kiam-certificates-using-cert-manager"
author = "Suraj Narwade"
date = "2021-04-23T06:51:25.501Z"
category = "Blog"
+++

Source: <https://unsplash.com/photos/pZld9PiPDno>


When associating IAM roles with pods instead of assigning a super role to worker nodes, Kiam is a fairly popular project in Community; kiam runs as an agent on each node in the Kubernetes cluster, allowing cluster users to associate IAM roles to Pods.


To enhance security, Kiam uses certificates for servers \& agents. To simplify the certificate management for Kiam, we will utilise cert\-manager,


cert\-manager is a native Kubernetes certificate management controller that helps issue certificates from various sources or self\-signed ones.


For Kiam, we will need the following:


* Self\-signed CA
* Server Certificate (Generated from above CA)
* Agent Certificate (Generated from above CA)


Following Code shows the issuer \& Certificate for the initial Self\-signed CA. **isCA** option in the certificate spec specifies that it’s a CA certificate.



```
---
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: kiam-ca-selfsigned-issuer
spec:
  selfSigned: {}
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: kiam-ca-selfsigned
spec:
  secretName: kiam-ca-selfsigned
  commonName: "kiam-ca"
  isCA: true
  issuerRef:
    name: kiam-ca-selfsigned-issuer
  usages:
  - "any"

```

The above CA certificate will be stored in Kiam\-ca\-self\-signed secret.


Based on this CA certificate, we will create an Issuer of CA type and pass the secret name.



```
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: kiam-ca-issuer
spec:
  ca:
   secretName: kiam-ca-selfsigned

```

Now that we have an issuer, we are ready to generate server \& agent certificates,


### Generating Server \& Agent Certificates


* For Server



```
  apiVersion: cert-manager.io/v1
  kind: Certificate
  metadata:
    name: kiam-server
  spec:
    secretName: kiam-server-tls
    issuerRef:
      name: kiam-ca-issuer
    usages:
    - "any"
    dnsNames:
    - "localhost"
    - "kiam-server"
    ipAddresses:
    - "127.0.0.1"

```
* For Agent



```
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: kiam-agent
spec:
  secretName: kiam-agent-tls
  commonName: agent
  issuerRef:
    name: kiam-ca-issuer
  usages:
  - "any"

```

### Renewing Certificates


The only caveat here is if the main Self\-signed CA issuer gets renewed, the following server \& agent certificates don’t get renewed; this issue is already reported on [cert\-manager](https://github.com/jetstack/cert-manager/issues/2478) repositories. You’ll have to renew them by following commands manually,



```
kubectl cert-manager renew kiam-server --namespace=kube-system
kubectl cert-manager renew kiam-agent --namespace=kube-system

```

I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


### References


* <https://github.com/uswitch/kiam/blob/master/docs/TLS.md>
* [https://github.com/jetstack/cert\-manager/issues/2478](https://github.com/jetstack/cert-manager/issues/2478)


