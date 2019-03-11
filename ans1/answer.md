## Exercise 1 - First pods

In the following exercises use `centos:7` image.

1.  Create pod that only runs `sh -c '(tail -f /dev/null)'`. Name this pod `tailpod`.
2.  Create pod that prints out "Hello world" to its log and exits. Linux command to achieve this is `echo Hello world`. Print the log with `oc logs <podname>`. Name this pod `hellopod`.

## Answer

1.  `oc create -f tailpod.yaml`

    *`tailpod.yaml`*:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: tailpod
      labels:
        job: analyze
    spec:
      containers:
      - name: container-a
        image: centos:7
        command:
        - sh
        - -c
        - (tail -f /dev/null)
    ```

2.  `oc create -f hellopod.yaml`, `oc logs hellopod`

    *`hellopod.yaml`*:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hellopod
      labels:
        job: analyze
    spec:
      restartPolicy: never
      containers:
      - name: container-a
        image: centos:7
        command:
        - sh
        - -c
        - (echo Hello world)
    ```
