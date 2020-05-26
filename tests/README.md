# Kruize Tests

Kruize Functional tests check if kruize generates recommendations for the applications it monitors. It also validates the endpoints /recommendations and /listApplications. 

We validate kruize recommendations in various scenarios as described below and on environments like docker and other kubernetes clusters (Openshift, minikube and ICP (IBM Cloud Private)). 
Below is a brief description of the tests:


## Monitor a new application 

Here we test the kruize APIs recommendations and listApplications by deploying a new application. The test does the following:
- Deploys kruize and its dependencies using deploy.sh from the kruize repo
- Deploy acmeair application
- Run the jmeter workload
- Validates kruize recommendations for the new application
- Also validates the list of Applications that kruize monitors


## Remove an application that was deployed

This test validates the status of an application that was terminated. 


## Multiple Appplications  

Here we test the kruize APIs recommendations and listApplications by deploying both acmeair and spring-petclinic applications. The test does the following:
- Deploys kruize and its dependencies using deploy.sh from the kruize repo
- Deploys acmeair application
- Deploys petclinic application
- Runs the jmeter workload for both these applications
- Validates kruize recommendations for both these applications 
- Also validates the list of Applications that kruize monitors


## Multiple Instances/Deployments of an Application

Here we test the kruize APIs recommendations and listApplications by deploying both acmeair and spring-petclinic applications. The test does the following:
- Deploys kruize and its dependencies using deploy.sh from the kruize repo
- Deploys acmeair application
- Deploys petclinic application
- Runs the jmeter workload for both these applications
- Validates kruize recommendations for both these applications 
- Also validates the list of Applications that kruize monitors


### Prerequisites for running the tests:

- Minikube setup 
- Openshift setup
- ICP setup
- Tools like docker, kubectl, oc, cloudctl and jq 

### To run the tests:

First, cleanup any previous instances of kruize and the applications using the below command:

```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac <CLUSTER_TYPE> -t
```

Use the below command to run the test:

```
$ <KRUIZE_REPO>/tests/test-kruize.sh [-k kurl ] [-c [docker|icp|minikube|openshift]] [-i docker-image] [-s|t] [-u user] [-p password] [-n namespace] [--timeout=x, x in seconds, for docker only] -r [location of rt-cloud-benchmarks] [--resultsdir=results directory] [ --tctype=functional/sanity]   -s = start(default) -t = terminate

	--tctype=functional is the default which runs all the above tests.
	--tctype=sanity just runs the first test as a basic sanity test for kruize.
```

#### On Docker

Cleanup any previous instances of kruize and the applications using the below command:

```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac docker -t
```


Use the below command to run the test:

```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac docker -i kruize/kruize:0.7.0 
```


#### On Minikube

Cleanup any previously deployed instances of kruize and the applications using the below command:

```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac minikube  -k http://<Minikube Server IP> -t
```

Use the below command to run the test:
```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac minikube -i kruize/kruize:0.7.0 -k http://<Minikube Server IP> -n monitoring
```

#### On Openshift

Cleanup any previously deployed instances of kruize and the applications using the below command:
```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac openshift -k <Fully qualified Server name> -u kubeadmin -p <password> -n openshift-monitoring -t
```

Use the below command to run the test:
```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac openshift -i kruize/kruize:0.7.0 -k kruize-oc.xx.yy.com -u kubeadmin -p <password> -n openshift-monitoring
```

#### On ICP

Cleanup any previously deployed instances of kruize and the applications using the below command:
```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac icp -i kruize/kruize:0.7.0 -k https://<Server IP>:8443 -u admin -p <password> -n kube-system -t
```

Use the below command to run the test:
```
$ <KRUIZE_REPO>/tests/test-kruize.sh -ac icp -i kruize/kruize:0.7.0 -k https://<Server IP>:8443 -u admin -p <password> -n kube-system
```
