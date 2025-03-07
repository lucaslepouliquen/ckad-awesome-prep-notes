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

