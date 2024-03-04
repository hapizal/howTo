#

we can create or apply a YAML file using the kubectl apply -f <filename.yaml> command, which will create the Deployment in wer Kubernetes cluster according to the specifications defined in the YAML file. \
Similarly, we can use YAML files to update or delete Deployments, depending on the changes we want to make to the cluster’s desired state.

A typical Kubernetes Deployment YAML file includes the following key components:

- apiVersion: Specifies the Kubernetes API version, such as “apps/v1” for Deployments.
- kind: Specifies the type of Kubernetes resource, in this case, “Deployment.”
- metadata: Provides metadata for the Deployment, including the name, labels, and annotations.
- spec: Defines the desired state of the Deployment, including the number of replicas, the pod template, and any other related specifications. It includes:
- replicas: Specifies the desired number of identical pod replicas to run.
- selector: Specifies the labels that the Replica Set uses to select the pods it should manage.
- template: Contains the pod template used for creating new pods, including container specifications, image names, and container ports.

example
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: example-image
        ports:
        - containerPort: 8080

```

- apiVersion: Specifies the Kubernetes API version. In this case, it’s using the “apps/v1” API version, which is appropriate for Deployments.
- kind: Specifies the type of Kubernetes resource. Here, it’s “Deployment,” indicating that this configuration file is defining a Deployment.
- spec: This section defines the desired state of the Deployment.
- replicas: 3: Specifies that we want to run three replicas of wer application.
- selector: Describes the selector to match pods managed by this Deployment.
- matchLabels: Specifies the labels that the Replica Set created by the Deployment should use to select the pods it manages. In this case, pods with the label app: example are selected.
- template: Defines the pod template used for creating new pods.
- metadata: Contains the labels to apply to the pods created from this template. In this case, the pods will have the label app: example.
- spec: Describes the specification of the pods.
- containers: This section specifies the containers to run in the pod.
- name: example-container: Assigns a name to the container.
- image: example-image: Specifies the Docker image to use for this container.
- ports: Defines the ports to open in the container.
- containerPort: 8080: Indicates that the container will listen on port 80.

# Kubernetes deployment YAML - Examples
Let’s look at some Kubernetes Deployment YAML examples.

Example 1 — Run NGINX Containers using a YAML File
This will create a deployment running 3 NGINX pods.

nginx-deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
```

# Example 2 — Pass environment variables in Kubernetes deployment YAML 
we can pass environment variables to containers in a Kubernetes Deployment YAML to configure application settings, provide secrets, or customize the behavior of wer application.

Environment variables are often used to decouple configuration from the container image, making it more flexible and easier to manage. To define environment variables in a Deployment YAML file, we can use the env field under the containers section.

env-deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: my-app-container
        image: my-app-image
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: db.example.com
        - name: API_KEY
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: api-key
```

The above example shows how to use environment variables in wer deployment.

However, it’s important to consider when and why we should not use environment variables!

Avoid using environment variables for sensitive information like passwords, API keys, or access tokens. Environment variables can be viewed by anyone with access to the pod, which poses a security risk. Instead, use Kubernetes Secrets to store and securely mount sensitive data into containers.

If our application configuration becomes too complex or has multiple layers of settings, managing all of them through environment variables may become unwieldy. In such cases, consider using configuration files, ConfigMaps, or Custom Resource Definitions (CRDs) to manage more structured and extensive configurations.

When we need to update configuration without restarting the application, environment variables might not be the best choice, as we typically need to redeploy the pod to apply changes. Consider using external configuration management tools, like Consul, etcd, or a configuration management system like Helm, to achieve dynamic updates.

If we have a large number of environment variables, it can be challenging to manage them in a readable and maintainable way within a YAML file. In such cases, we might prefer other methods like ConfigMaps or configuration files.

# Example 3 — Kubernetes deployment YAML with multiple replicas
To specify multiple replicas in wer YAML file, simply modify the replicas section to the number we desire:

spec:
  replicas: 10
Example 4 — Kubernetes deployment YAML with resource limits
Setting resource limits helps K8s manage the resources allocated to each container, ensuring that they do not exceed the specified limits and can be scheduled on appropriate nodes.

Building on our previous Example 1 for an NGINX deployment, we can add the resources section:

nginx-deployment-with-resource-limits.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            memory: "256Mi"  # Maximum memory allowed
            cpu: "200m"       # Maximum CPU allowed (200 milliCPU)
          requests:
            memory: "128Mi"  # Initial memory request
            cpu: "100m"       # Initial CPU request

```
resources: This section is used to define resource requests and limits for the container.
limits: Specifies the maximum amount of CPU and memory that the container is allowed to use. In this example, the container is limited to a maximum of 256 MiB of memory and 200 milliCPU (0.2 CPU cores).
requests: Specifies the initial amount of CPU and memory that the container requests when it starts. In this example, the container requests 128 MiB of memory and 100 milliCPU (0.1 CPU cores) initially.
we can find more on limits and requests on the official documentation pages.

# Example 5 — Kubernetes deployment YAML with health checks

Again building on Example 1, we can add some health checks to our deployment YAML to include livenessProbe and readinessProbe settings.

nginx-deployment-with-health-checks.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /                # The path to check for the liveness probe
            port: 80               # The port to check on
          initialDelaySeconds: 15  # Wait this many seconds before starting the probe
          periodSeconds: 10        # Check the probe every 10 seconds
        readinessProbe:
          httpGet:
            path: /                # The path to check for the readiness probe
            port: 80               # The port to check on
          initialDelaySeconds: 5   # Wait this many seconds before starting the probe
          periodSeconds: 5         # Check the probe every 5 seconds
```

livenessProbe: The liveness probe checks whether the container is still alive. It uses an HTTP GET request to the / path on port 80 of the container. If the probe fails, K8s will restart the container.
readinessProbe: The readiness probe checks whether the container is ready to serve traffic. It also uses an HTTP GET request to the / path on port 80 of the container. If the probe fails, the container is marked as not ready, and K8s won’t send traffic to it.
Many other configurations can be applied using health checks.

# Example 6 — Kubernetes deployment YAML with persistent volumes
Building on Example 1, we can modify the deployment YAML to add a volumes section, in which we define a Kubernetes Persistent Volume Claim (PVC). The PVC defines the storage requirements, and a Persistent Volume (PV) is dynamically provisioned or statically assigned to meet those requirements.

nginx-deployment-with-pvc.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: my-pvc  # Name of the Persistent Volume Claim
```
A PVC is defined using persistentVolumeClaim. In this case, it’s referenced by the name my-pvc.

The my-pvc PVC must be defined separately in another YAML file. The Deployment’s container can then mount the volume specified by the name: data in the volumes section.

mypvc.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi  # Request 1 Gigabyte of storage
```
accessModes specifies the access mode for the volume. It’s set to ReadWriteOnce, indicating that it can be mounted in read-write mode by a single node at a time.
resources.requests.storage specifies the amount of storage requested. In this example, it requests 1 gigabyte of storage.
Don’t forget to apply both files in this case using the kubctl -f apply commands.
```
kubectl apply -f mypvc.yaml
kubectl apply -f nginx-deployment-with-pvc.yaml
```

# Example 7 — Kubernetes deployment YAML with affinity settings
To specify affinity settings in our deployment, we can add the affintity section under spec. Here we can specify node affinity and pod anti-affinity settings.

nginx-deployment-with-affinity.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-type
                operator: In
                values:
                - nginx-node
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - nginx
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
```
With these affinity settings, Kubernetes will attempt to schedule the NGINX Pods on nodes labeled as nginx-node, and it will make sure that no two NGINX Pods with the label app: nginx run on the same node, promoting fault tolerance and reliability in wer NGINX deployment.

nodeAffinity is used to ensure that the Pods are scheduled only on nodes with a specific label. Pods will be required to be scheduled on nodes with the label node-type: nginx-node.
podAntiAffinity is used to ensure that no two NGINX Pods with the label app: nginx are scheduled on the same node. The topologyKey specifies that the scheduling is based on the hostname of the nodes.
Key points
