## Remote debugging a kubernetes pod

### Use local images in minikube
```
minikube start
eval $(minikube -p minikube docker-env)
docker images
```
cd to remote-debug directory and run `mvn clean install` to build the image. In the helm template, set imagePullPolicy to never.