## Exercise 2 - Persistent volumes and remote shell

* Create a persistent volume of size 1 GiB. 
* Create a pod that runs `sh -c '(tail -f /dev/null)'` and mounts the newly created volume to `/data`. Name this pod to be `tailpod-pv`.
* Connect to the pod with `oc rsh`.
* Print "Hello, world" to `/data/hello.txt` and to `/hello.txt` (`echo Hello, world > /data/hello.txt`, `echo Hello, world > /tmp/hello.txt`) inside the pod.
* Delete the pod, create it again, connect to it with `oc rsh` and see if the files still exist (`ls /`, `ls /data/`)

## Answer

*   Create the persistent volume claim: `oc create -f pvc.yaml`

    *`pvc.yaml`*:

    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pvc-a
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
    ```

*   Create the pod: `oc create -f tailpod-pv.yaml`

    *`tailpod-pv.yaml`*:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: tailpod-pv
      labels:
        job: analyze
    spec:
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
        - (tail -f /dev/null)
        volumeMounts:
        - mountPath: /data
          name: volume-a
    ```

*   Connect to pod: `oc rsh tailpod-pv`

*   Create files in PVC and container

    ```bash
    sh-4.2$ echo "Hello, world" > /data/hello.txt
    sh-4.2$ echo Hello, world > /tmp/hello.txt
    sh-4.2$ exit
    ```

*   Delete & re-create pod, see if files still exist

    ```bash
    $ oc replace --force -f tailpod-pv.yaml
    $ oc rsh tailpod-pv
    sh-4.2$ cat /tmp/hello.txt
    cat: /tmp/hello.txt: No such file or directory
    sh-4.2$ cat /data/hello.txt
    Hello, world
    sh-4.2$ exit
    ```
