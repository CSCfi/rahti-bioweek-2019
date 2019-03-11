# Rahti exercises

## Exercise 0 - Preparations

1. Log in to Rahti at [rahti.csc.fi:8443](https://rahti.csc.fi:8443/) with the training account
2. Authorize the `oc` command line tool
   * [Instructions](https://rahti.csc.fi/tutorials/elemental_tutorial/#preparations)
3. Create project in Rahti
   * Name the project as `<lastname-firstname-bioweek>`
   * [Instructions](https://rahti.csc.fi/tutorials/elemental_tutorial/#Projects)

## Exercise 1 - First pods

In the following exercises use `centos:7` image.

1.  Create pod that only runs `sh -c '(tail -f /dev/null)'`. Name this pod `tailpod`.
2.  Create pod that prints out "Hello world" to its log and exits. Linux command to achieve this is `echo Hello world`. Print the log with `oc logs <podname>`. Name this pod `hellopod`.

## Exercise 2 - Persistent volumes and remote shell

* Create a persistent volume of size 1 GiB. 
* Create a pod that runs `sh -c '(tail -f /dev/null)'` and mounts the newly created volume to `/data`. Name this pod to be `tailpod-pv`.
* Connect to the pod with `oc rsh`.
* Print "Hello, world" to `/data/hello.txt` and to `/hello.txt` (`echo Hello, world > /data/hello.txt`, `echo Hello, world > /tmp/hello.txt`) inside the pod.
* Delete the pod, create it again, connect to it with `oc rsh` and see if the files still exist (`ls /tmp/`, `ls /data/`)

## Exercise 3 - Download and analyze fission yeast genome with aragorn

Make two pods and a persistent volume claim (or use the one from Exercise 2). Remember to define `restartPolicy` so that the pods won't rerun the containers over and over again.

*   One pod should download and unzip fission yeast genome FASTA file to a persistent volume:

    ```bash
    cd /data/ && curl -O https://object.pouta.csc.fi/rahti-bioweek/GCF_000002945.1_ASM294v2_genomic.fna
    ```

    For this pod, use image `centos:7`.

    Genome originally downloaded from `https://www.ncbi.nlm.nih.gov/genome/?term=Schizosaccharomyces%20pombe[Organism]&cmd=DetailsSearch`

*   Second pod should execute `aragorn /data/GCF_000002945.1_ASM294v2_genomic.fna`. See the output of this run with `oc logs` command.

    For this pod use image `biocontainers/aragorn:v1.2.38-1-deb_cv1`.

*   Execute the above again, this time redirecting the output of `aragorn` to file `/data/analysis.txt`. Copy the file `/data/analysis.txt` from the pvc to your local filesystem. Hint: `oc rsync` from the `tailpod-pv` that still should be running.
