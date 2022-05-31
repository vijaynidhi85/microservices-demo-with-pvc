
# Sock Shop with Storage Persistence : A Microservice Demo Application

This project is designed to modify the original Sock Shop's microservice application to user Persistant volumes for kubernetes based deployments rather than tmp directories.

Please refer to the original project here:

https://microservices-demo.github.io/

and

https://github.com/microservices-demo/microservices-demo
# Deployment instructions :
* clone the repo to you local setup where you have kubeconfig configured to your kubernetes cluster
* cd into **deploy/kubernetes/manifests ** directory
* run following command to deploy all yamls in the manifests
```
kubectl apply -f . 
```

# Note
* These changes are made for the kubernetes based deployment to use back-end storage based CSI provisioners for 3 DB services(carts/catalogue/orders)
* It uses the default Storage-Class 
* This is for testing/demo purpose only - made some security context changes to run on openshift
* Tested on openshift and nutanix karbon k8s
* for openshift you may need to add the default account of the sock-shop namespace to priviledge security context

```
oc adm policy add-scc-to-user privileged -z default -n sock-shop
```
* You might hit docker image pull limits - you could try to authenticate your image pulls by performing the following:

1. docker login ## login to your docker hub account
2. Create the docker registry pull secret

```
kubectl create secret generic regcred     --from-file=.dockerconfigjson=~/.docker/config.json     --type=kubernetes.io/dockerconfigjson -n sock-shop
```
3. Patch the default serfvice account of the namespace sock-shop

```
 kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "regcred"}]}'
 ```
 4. Delete the pods with imagepull backoff issues
 
# Changes Made:
* Modified the following files in the manifests directory:

  * 03-carts-db-dep.yaml
  * 07-catalogue-db-dep.yaml
  * 13-orders-db-dep.yaml

* For each of the above files - added PVC definition and mounted it in the respective db folder
* Fixed a few security context settings to run on Openshift 


--------------------


# Sock Shop : A Microservice Demo Application

The application is the user-facing part of an online shop that sells socks. It is intended to aid the demonstration and testing of microservice and cloud native technologies.

It is built using [Spring Boot](http://projects.spring.io/spring-boot/), [Go kit](http://gokit.io) and [Node.js](https://nodejs.org/) and is packaged in Docker containers.

You can read more about the [application design](./internal-docs/design.md).

## Deployment Platforms

The [deploy folder](./deploy/) contains scripts and instructions to provision the application onto your favourite platform. 

Please let us know if there is a platform that you would like to see supported.

## Bugs, Feature Requests and Contributing

We'd love to see community contributions. We like to keep it simple and use Github issues to track bugs and feature requests and pull requests to manage contributions. See the [contribution information](.github/CONTRIBUTING.md) for more information.

## Screenshot

![Sock Shop frontend](https://github.com/microservices-demo/microservices-demo.github.io/raw/master/assets/sockshop-frontend.png)

## Visualizing the application

Use [Weave Scope](http://weave.works/products/weave-scope/) or [Weave Cloud](http://cloud.weave.works/) to visualize the application once it's running in the selected [target platform](./deploy/).

![Sock Shop in Weave Scope](https://github.com/microservices-demo/microservices-demo.github.io/raw/master/assets/sockshop-scope.png)

## 
