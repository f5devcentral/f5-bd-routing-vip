# Goal

The goal of this project is to have a routing VIP handling L7 routes where each L7 route should be able to have its own independent advanced services configuration. This project demonstrates the extended flexibility of BIG-IP when regular mechanisms don't meet requirements.

# Motivation

BIG-IP Controller (aka Container Connector) for Kubernetes and Openshift provides great features, these include:

* Dynamically creates, manages, and destroys BIG-IP objects.
* Forwards traffic from the BIG-IP device to Kubernetes clusters via NodePort or ClusterIP.
* Support for F5 iApps.
* Handles F5-specific VirtualServer objects created in Kubernetes.
* Handles standard Kubernetes Ingress objects using F5-specific extensions.
* Handles OpenShift Route objects using F5-specific extensions.

Please see the user documentation / http://clouddocs.f5.com/containers/latest/kubernetes/index.html for more information 

The routing-vip offered in this project uses the iApp support of BIG-IP Controller to provide following additional features:

1. L7 routes in Kubernetes with advanced BIG-IP services such as iRules, ASM (ie: WAF), AFM (ie: firewall rules, DDoS), IP-intelligence, High Speed Logging, etc... deployed with a ConfigMap
2. Allow per L7 route use of advanced BIG-IP services mentioned above.  

Although feature 1) is possible with regular L7 routes in Openshift these are not possible in Kubernetes Ingress routes. Also, please note that when deploying iApps with F5 ConfigMaps by default it is not provided a mechanism to allow sharing a VIP across several L7 routes.

# Overall architecture

Typically we will have a HTTP VIP that will redirect to an HTTPS VIP which will host the L7 routes implemented with LTM policies. This configuration is just an example and could be adapted to any other requirements.

![Conceptual view](https://raw.githubusercontent.com/wiki/f5devcentral/f5-bd-routing-vip/images/diagram_conceptual_view.png)

An iApp called routing-vip iApp (f5bd.routing-vip to be precise) will implement the HTTP & HTTPS VIPs and the LTM policies with a default LTM policy rule. We will have only one instance of this iApp per HTTP/HTTPS VIP set. This is deployed with a ConfigMap.

We will attach LTM policy rules to this LTM policy by using another iApp named f5bd.route. This will define the matching criteria and the actions to be taken for a given L7 route. There will be one instance of the f5bd.route iApp for each L7 route. In addition of creating the LTM policy rule the f5bd.route also creates the pool of the associated L7 route. each f5bd.route instance is deployed with a ConfigMap and gets updated automatically by Openshift/Kubernetes.

This implementation is exhibit in the next diagram.

![Implementation view](https://raw.githubusercontent.com/wiki/f5devcentral/f5-bd-routing-vip/images/diagram_implementation_view.png)

Please note that there is no limitation on how many intances of the routing-vip iApp are deployed.

# Using the routing-vip & feedback

Please check the [wiki page for documentation on how to use this routing-vip](https://github.com/f5devcentral/f5-bd-routing-vip/wiki).

Also, I would love to hear from you. Please fill free to fill issues reports or feature request through this link. If you prefer you can also contact me directly sending me an e-mail to the following address:

![Contact](https://raw.githubusercontent.com/wiki/f5devcentral/f5-bd-routing-vip/images/contact.png)

# Future work

If there is demand from F5 users I could enhance the routing-vip to allow A/B deployments in the same way Openshift L7 routes implement this feature. If you are intersted in this feature please fill an issue in this link.


