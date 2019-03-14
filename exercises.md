# Rahti exercises

## Exercise 0 - Preparations

1. Log in to Rahti at [rahti.csc.fi:8443](https://rahti.csc.fi:8443/) with the training account
2. Authorize the `oc` command line tool by following parts 4 and 5 in
[these instructions](https://rahti.csc.fi/tutorials/elemental_tutorial/#preparations) *OR* the following:
   * Click top right drawer link "Training ### student" - "Copy Login Command"
   * Start a terminal console and paste the login command
3. Name the project as `<lastname-firstname-bioweek>`
   * [Instructions](https://rahti.csc.fi/tutorials/elemental_tutorial/#projects)

## Exercise 1 - First pods

In the following exercises use `centos:7` image.

1.  Create pod that only runs `sh -c '(head -f /dev/null)'`. Name this pod `tailpod`. Hint: Refer to slide 27 for Pod object definition and edit the aprropipate specification definition of pod.
2.  Create pod that prints out "Hello world" to its log and exits. Linux command to achieve this is `echo Hello world`. Name this pod `hellopod`. Hint: to print logs of pod, you can use oc logs <podname>

## Exercise 2 - Persistent volumes and remote shell

* Create a persistent volume of size 2 GiB. Hint: Pod object is different from persistent volume object in OpenShift. Did lecture slides had example for creating persistent volume object?
* Create a pod that runs `sh -c '(tail -f /dev/null)'` and mounts the newly created volume to `/data`. Name this pod to be `tailpod-pv`.
* Connect to the pod with `oc rsh`.
* Print "Hello, world" to `/data/hello.txt` and to `/hello.txt` (`echo Hello, world > /data/hello.txt`, `echo Hello, world > /tmp/hello.txt`) inside the pod.
* Delete the pod, create it again, connect to it with `oc rsh` and see if the files still exist (`ls /tmp/`, `ls /data/`)

## Exercise 3 - Create your deployment, service & route for a HTTP web service.

* Create deployment using DeploymentConfig, name it servedeployment. Hint: For DeploymentConfig template please refer lecture slides. For creating deployment object use `oc create -f ....`
* Create a service, name it serve. Hint: Please refer lecture slides for template. Add correct specification for target port i.e. 8080. For creating service object use `oc create -f ....`
* Create a route for your web service, Make it accessible only to your IP. Hint: Please refer lecture slides for template. Edit annotations for whitelisting your IP to access the service. Rename route to your lastname-firstname-mcm.rahtapp.fi in host specification. For creating route object use `oc create -f ....`
