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

## Exercise 3 - Download and analyze fission yeast genome with aragorn

Make two pods and a persistent volume claim (or use the one from Exercise 2). Remember to define `restartPolicy` so that the pods won't rerun the containers over and over again.

*   One pod should download fission yeast genome FASTA file to a persistent volume:

    ```bash
    cd /data/ && curl -O https://object.pouta.csc.fi/rahti-bioweek/GCF_000002945.1_ASM294v2_genomic.fna
    ```

    For this pod, use image `centos:7`.

    Genome originally downloaded from `https://www.ncbi.nlm.nih.gov/genome/?term=Schizosaccharomyces%20pombe[Organism]&cmd=DetailsSearch`

*   Second pod should execute `aragorn /data/GCF_000002945.1_ASM294v2_genomic.fna`. See the output of this run with `oc logs` command.

    For this pod use image `biocontainers/aragorn:v1.2.38-1-deb_cv1`.

*   Execute the above again, this time redirecting the output of `aragorn` to file `/data/analysis.txt`. Copy the file `/data/analysis.txt` from the pvc to your local filesystem. Hint: `oc rsync` from the `tailpod-pv` that still should be running.

*   **Extra**: Use `initContainer` to download the input genome. See [rahti documentation](https://rahti.csc.fi/tutorials/patterns/#initcontainer) for more information on initcontainers.
