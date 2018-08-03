# Activiti Cloud Application Full example chart

This example includes all the building blocks that conforms an Activiti Cloud application, such as:

- Gateway (Spring Cloud) 
- SSO/IDM (Keycloak)
- Activiti Cloud Runtime Bundle (Example)
- Activiti Cloud Connector (Example)
- Activiti Cloud Query Service
- Activiti Cloud Audit Service

# Prerequisites

Depending on which flavour of Kubernetes you want to run your examples you will need some preparation steps. 
In general you will need to make sure that you have an external DNS service set up to be able to register and access Activiti Cloud Services. This usually involve to have a Kubernetes Ingress Controller. Look at the following sections about different Cloud Providers and setups before installing the Activiti Cloud Example chart.

## Jenkins X
You can get your DNS name by doing 
```
jx env dev
jx get urls
```
You’ll see url of the form http://jenkins.jx.<CLUSTER_IP>

## PKS
PKS doesn't come with an Ingress Controller so you will need to manually install it. You can do this with HELM:

```helm install stable/nginx-ingress```

Once you get your Ingress Controller up and running you will need to get its external IP address, and you can use a service such as [nip.io](http://nip.io) as DNS service. This will allow you to access your services by name under the same domain. 

In order to get the Ingress Controller external IP you can run:
> kubectl get service

You will need to copy the EXTERNAL-IP from your controller and now you can use NIP.io by pointing to your services at:
```<SERVICE-NAME>.<EXTERNAL-IP>.nip.io```

# Installation
Before installing the chart you will need to provide a values.yaml file (myvalues.yaml) which you can copy from the file in this directory and update with your DNS name. Look for <DNS name> inside the values.yaml file and replace accordingly with your DNS.

You can install this chart by running against a Kubernetes Cluster:

```
helm repo add activiti-cloud-charts https://activiti.github.io/activiti-cloud-charts/
helm repo update
helm install -f myvalues.yaml activiti-cloud-full-example
```


# Interacting with the services

You can use a postman collection to explore the example. Download https://github.com/Activiti/activiti-cloud-examples/blob/master/Activiti%20v7%20REST%20API.postman_collection.json and import the collection into your postman (we recommend installing the app and not using the chrome one). Set the value of the gateway to http://activiti-cloud-gateway.<SPECIFIC_TO_YOUR_CLUSTER> and idm to http://activiti-keycloak.<SPECIFIC_TO_YOUR_CLUSTER>

Call the refresh endpoint in gateway to refresh it.
Call the getKeycloakToken endpoint to get a keycloak token.
Call startProcess to start a process instance.
Call queryProcessInstances in query to check that query can see the process instance. Call getEvents in audit to check that the event was audited. You should also see this process instance in the UI that you checked in iv.

The example is a starting-point - plug in your images if you have them and add further runtime-bundles and connectors to customise.

# Other configurations

There is a flag in the values.yaml to enable a demo ui if desired and commented sections that can be uncommented to enable security policies.

To enable postgres rather than h2 set db.deployPostgres. See individual charts for more options.

If you don't want to use DNS then remove the Ingresses and instead set the Services to type LoadBalancer. It would then be necessary to deploy to find out the IPs and then update the values.yaml after you've found out the IPs (from kubectl get services) and update the helm release with `helm upgrade`.