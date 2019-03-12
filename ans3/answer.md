
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

## Answer

*   `oc create -f curlpod.yaml`

    *`curlpod.yaml`*:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: curlpod
      labels:
        job: analyze
    spec:
      restartPolicy: Never
      volumes:
      - name: volume-a
        persistentVolumeClaim:
          claimName: pvc-a
      containers:
      - name: container-a
        image: centos:7
        command:
        - sh
        - -c
        - > 
           (cd /data/ && 
           curl -O https://object.pouta.csc.fi/rahti-bioweek/GCF_000002945.1_ASM294v2_genomic.fna)
        volumeMounts:
        - mountPath: /data
          name: volume-a
    ```

*   `oc create -f arapod.yaml`

    *`arapod.yaml`*:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: arapod
      labels:
        job: analyze
    spec:
      restartPolicy: Never
      volumes:
      - name: volume-a
        persistentVolumeClaim:
          claimName: pvc-a
      containers:
      - name: container-a
        image: biocontainers/aragorn:v1.2.38-1-deb_cv1
        command:
        - sh
        - -c
        - (aragorn /data/GCF_000002945.1_ASM294v2_genomic.fna)
        volumeMounts:
        - mountPath: /data
          name: volume-a
    ```

*   `oc logs arapod`


*   `oc create -f arapod-file.yaml`

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: arapod
      labels:
        job: analyze
    spec:
      restartPolicy: Never
      volumes:
      - name: volume-a
        persistentVolumeClaim:
          claimName: pvc-a
      containers:
      - name: container-a
        image: biocontainers/aragorn:v1.2.38-1-deb_cv1
        command:
        - sh
        - -c
        - (aragorn /data/GCF_000002945.1_ASM294v2_genomic.fna > /data/analysis.txt)
        volumeMounts:
        - mountPath: /data
          name: volume-a
    ```

    Copy the file to local filesystem from `tailpod-pv` Pod: `oc rsync tailpod-pv:/data/analysis.txt ./`

*   **Extra**

    *`curl-ara-pod.yaml`*:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: curl-ara-pod
      labels:
        job: analyze
    spec:
      restartPolicy: Never
      volumes:
      - name: volume-a
        persistentVolumeClaim:
          claimName: pvc-a
      initContainers:
      - name: curl-container
        image: centos:7
        volumeMounts:
        - mountPath: /data
          name: volume-a
        command:
        - sh
        - -c
        - > 
           (cd /data/ && 
           curl -O https://object.pouta.csc.fi/rahti-bioweek/GCF_000002945.1_ASM294v2_genomic.fna)
      containers:
      - name: container-a
        image: biocontainers/aragorn:v1.2.38-1-deb_cv1
        command:
        - sh
        - -c
        - (aragorn /data/GCF_000002945.1_ASM294v2_genomic.fna)
        volumeMounts:
        - mountPath: /data
          name: volume-a
    ```
