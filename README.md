
# Ensure Immutability of Containers at Runtime

In this section, we will take a look at how to Ensure Immutability of Containers at Runtime

-  Although containers are meant to be immutable by default, we can carry out in-place updates on them.

- Now, this can be done in a number of different ways – such as copying files directly in to the pod or getting a shell in to the container and making changes e.t.c.

      docker cp nginx.conf nginx:/etc/nginx

    Or

      kubectl exec -it nginx -- apt update

- To prevent this, make sure that we cannot write to the file system once the pod is started.

- To do this we can use **`security contexts`** with **`readOnlyRootFilesystem=true`**


### Nginx with readOnlyRootFilesystem=true to ensure the immutability

nginx.yaml

    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        run: nginx
      name: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        securityContext:
          readOnlyRootFilesystem: true
        volumeMounts:
        - name: cache-volume
          mountPath: /var/cache/nginx
        - name: runtime-volume
          mountPath: /var/run
      volumes:
        - name: cache-volume
          emptyDir: {}
        - name: runtime-volume
          emptyDir: {}

  create the nginx.ayml

      kubectl create -f nginx.yaml


  test immutability

    kubectl exec -it nginx -- apt update


Using security context in Kubernetes YAML files allows you to define security settings for Pods and containers, such as Linux capabilities, SELinux options, and more. Here's an example of how you can incorporate security context into a Kubernetes YAML file:

In this example:

- `runAsUser`: Specifies the user ID under which the container should run.
- `capabilities`: Allows you to add or drop Linux capabilities for the container. In this case, it adds the `NET_ADMIN` capability.
- `readOnlyRootFilesystem`: Mounts the container's root filesystem as read-only, preventing any write access to it.

You can adjust these settings according to your specific security requirements. Remember to apply the YAML file using `kubectl apply -f filename.yaml` once you've defined the security context.

