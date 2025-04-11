# ckad-awesome-prep-notes

Resources to pass CKAD (Certified Kubernetes Application Developer)

## Official Resources

- [CNCF CKAD Curriculum](https://github.com/cncf/curriculum)
- [CKAD Exam Guide](https://docs.linuxfoundation.org/tc-docs/certification/certified-kubernetes-application-developer-ckad)
- [Candidate Handbook](https://docs.linuxfoundation.org/tc-docs/certification/lf-candidate-handbook)
- [Kubernetes Documentation](https://kubernetes.io/docs/)

## Core Concepts

- Kubernetes Architecture
- Pods, ReplicaSets, Deployments
- Services, Networking
- ConfigMaps, Secrets
- Persistent Volumes
- Namespaces, Resource Quotas

## Practical Skills

- Using `kubectl` efficiently
- Creating and modifying YAML manifests
- Debugging applications
- Managing container logs
- Port-forwarding and accessing applications

## Practice Labs & Simulators

- [Killer.sh CKAD Simulator](https://killer.sh/ckad) (included with exam registration)
- [Katacoda Kubernetes Playground](https://www.katacoda.com/courses/kubernetes)
- [Kubernetes by Example](https://kubernetesbyexample.com/)
- [Play with Kubernetes](https://labs.play-with-k8s.com/)
- [KodeKloud CKAD Course](https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/)

## Free Resources

- [CKAD Exercises by dgkanatsios](https://github.com/dgkanatsios/CKAD-exercises)
- [Kubernetes the Hard Way by Kelsey Hightower](https://github.com/kelseyhightower/kubernetes-the-hard-way)
- [Kubernetes Tutorials](https://kubernetes.io/docs/tutorials/)
- [Kubernetes Slack Community](https://kubernetes.slack.com/)

## Paid Courses

- [Udemy "Kubernetes Certified Application Developer" by Mumshad Mannambeth](https://www.udemy.com/course/certified-kubernetes-application-developer/)
- [Linux Foundation "Kubernetes for Developers" (LFD259)](https://training.linuxfoundation.org/training/kubernetes-for-developers/)
- [A Cloud Guru CKAD Course](https://acloudguru.com/course/certified-kubernetes-application-developer-ckad)

## Exam Tips

### Before the Exam
- Set up your terminal:
  ```bash
  alias k=kubectl
  export do="--dry-run=client -o yaml"
  export now="--force --grace-period 0"
  ```
- Practice with vim/nano shortcuts
- Memorize kubectl imperative commands

### During the Exam
- Read questions carefully
- Use kubectl explain and kubectl --help
- Manage your time (19 questions in 2 hours)
- Use the bookmark feature for difficult questions
- Verify your answers by checking resources with kubectl get/describe

### Key Commands to Practice
```bash
# Create resources imperatively
k create deploy nginx --image=nginx --replicas=3 $do > nginx-deploy.yaml
k create cm config-name --from-literal=key=value
k create secret generic secret-name --from-literal=key=value

# Quick editing
k edit deploy nginx
k explain pod.spec.containers --recursive

# Debugging
k logs pod-name
k describe pod pod-name
k exec -it pod-name -- /bin/sh

# Context switching
k config get-contexts
k config use-context context-name
```

## Exam Curriculum Breakdown (as of 2023)

### 20% - Application Design and Build
- Define, build and modify container images
- Understand Jobs and CronJobs
- Understand multi-container Pod design patterns
- Utilize persistent and ephemeral volumes

### 20% - Application Deployment
- Use Kubernetes primitives to implement deployment strategies
- Understand deployments and how to perform rolling updates
- Use ConfigMaps and Secrets to configure applications
- Know how to scale applications

### 25% - Application Environment, Configuration and Security
- Discover and use resources that extend Kubernetes
- Understand authentication, authorization and admission control
- Understanding and defining resource requirements, limits and quotas
- Configure network policies

### 15% - Application Observability and Maintenance
- Understand API deprecations
- Implement probes and health checks
- Use built-in CLI tools to monitor Kubernetes applications
- Utilize container logs

### 20% - Services and Networking
- Demonstrate understanding of NetworkPolicies
- Provide and troubleshoot access to applications via services
- Use Ingress controllers and Ingress resources
- Configure TLS in an Ingress

## Important YAML Templates

### Basic Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

### Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
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
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

### Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

Good luck with your CKAD exam!

CONFIGURATION
Configuration covers how run-time 'config' data is provided to your applications running in k8s. This includes environment variables, config maps, secrets, etc. Other items that are pertinent to config are the service account and security contexts used to execute the containers. The below items are covered by this part of the curriculum.

Config Maps / Environment Variables
The exam is about application development and its support within Kubernetes. With that said, high on the list of objectives is setting up config options and secrets for your applications. To create the most basic config map with a key value pair, see below.

$ kubectl create configmap app-config --from-literal=key123=value123
configmap "app-config" created
There are many ways to map config map items to environment variables within a container process. One quick, but tricky (syntax) option is shown below. This would be for a simple nginx container.

spec:
  containers:
  - image: nginx
    name: nginx
    envFrom:
    - configMapRef:
        name: app-config
Here is another way to map a specific value to a specific environment variable value.

  containers:
  - image: nginx
    name: nginx
    env:
      - name: SPECIAL_APP_KEY
        valueFrom:
          configMapKeyRef:
            name: app-config
            key: key123
Now to verify it worked.

$ kubectl exec -it nginx /bin/bash
root@nginx:/# env
HOSTNAME=nginx
SPECIAL_APP_KEY=value123
KUBERNETES_PORT_443_TCP_PROTO=tcp
...
Security Contexts
Security contexts can be applied at either the pod or container level. Of course pod-level contexts apply to all containers within that pod. There are several ways of defining privileges and access controls with these contexts.

These concepts are covered well in the tasks section below, but here is a basic RunAs example from the doc that shows both pod and container contexts being used.

apiVersion: v1
kind: Pod
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: sec-ctx-demo
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      runAsUser: 2000
      allowPrivilegeEscalation: false
App Resource Requirements
Defining the memory and cpu requirements for your containers is something that should always be done. It allows for more efficient scheduling and better overall hygiene for your application environment. Again, covered well in the tasks section below, but here is a brief snippet for the standard mem/cpu specification.

apiVersion: v1
kind: Pod
spec:
  containers:
  - name: demo
    image: polinux/stress
    resources:
      limits:
        memory: 200Mi
        cpu: 200m
      requests:
        memory: 100Mi
        cpu: 100m
Now to verify:

$ kubectl describe po stress
Name:         stress
Namespace:    default
Labels:       run=stress
IP:           10.36.1.17
Containers:
  stress:
  Image:          polinux/stress
  Limits:
      cpu:     200m
      memory:  200Mi
    Requests:
      cpu:        100m
      memory:     100Mi
Secrets
To quickly generate secrets, use the --from-literal flag like this:

$ kubectl create secret generic my-secret --from-literal=foo=bar -o yaml --dry-run > my-secret.yaml
This produces the following secret that can then be consumed by your containers. The value is encoded.

apiVersion: v1
data:
  foo: YmFy
kind: Secret
metadata:
  creationTimestamp: null
  name: my-secret
Now create the secret:

$ kubectl create -f my-secret.yaml
secret "my-secret" created
Now have a look at it:

 kubectl get secret my-secret -o yaml
apiVersion: v1
data:
  foo: YmFy
kind: Secret
metadata:
  name: my-secret
  namespace: default
  uid: bae5b8d8-d01a-11e8-8972-42010a800002
type: Opaque
Decode it:

$ echo "YmFy" | base64 --decode
bar
Secrets can be mounted as data volumes or be exposed as environment variables to be used by a container in a pod. Here we'll mount our above secret as a volume.

### Basic Pod
apiVersion: v1
kind: Pod
metadata:
  name: secrets-test-pod
spec:
  containers:
  - image: nginx
    name: test-container
    volumeMounts:
    - mountPath: /etc/secret/volume
      name: secret-volume
  volumes:
  - name: secret-volume
    secret:
      secretName: my-secret

### Service Accounts
When pods are created by K8s they are provided an identify via the service account. In most cases, pods use the default service account, but it can be specified directly.

apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: build-robot
  ...
