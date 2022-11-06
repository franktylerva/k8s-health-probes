# k8s-health-probes
This application is an example of using k8s probes with a Spring Boot Application the correct way.  This example uses a minikube cluster but any k8s cluster should work.  The example has a dependency on postgresql, rabbitmq, and redis.  It demonstrates how to use k8s liveness and readiness probes to control the state the application on a k8s cluster.

Start a minikube cluster.
```
minkube start
```

Point your docker client to the minikube docker daemon.
```
eval $(minikube docker-env)
```

Build the docker image.
```
mvn spring-boot:build-image
```

Deploy the application to the k8s cluster
```
helm install k8s-health-probes ./helm
```

In a separate terminal, port forward the k8s-health-probes-helm service to localhost 8080
```
kubectl port-forward svc/k8s-health-probes-helm 8080:80
```

Query the actuator endpoints using these endpoint:
```
http://localhost:8080/actuator/health/liveness

http://localhost:8080/actuator/health/readiness
```

Scale the dependent applications with the following commands:
```
kubectl scale statefulsets k8s-health-probes-postgresql --replicas=1
kubectl scale statefulsets k8s-health-probes-rabbitmq  --replicas=1
kubectl scale statefulsets 8s-health-probes-redis-master --replicas=1
```