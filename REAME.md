## Remote debugging a kubernetes pod

### Use local images in minikube
```shell
minikube start
eval $(minikube -p minikube docker-env)
docker images
```
cd to remote-debug directory and run `mvn clean install` to build the image. In the helm template, set imagePullPolicy to never.

### Package and install chart
```shell
helm package distribution/helm/remote-debug
helm upgrade --install -n tc remote-debug remote-debug-1.0-SNAPSHOT.tgz
```
When installing chart, javaOpts should look like this
```shell
debug.javaOpts="-agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=*:5005"
```
Note `suspend=y` which causes the Java class to pause on startup, allowing the remote debugging session to be started in Intellij.

### Run class with debugger attached
```shell
kubectl exec --stdin --tty -n tc <pod> -- sh
```
Inside pod
```shell
java -cp "app.jar" $JAVA_OPTS
```
Will suspend on startup and show in logs:
```shell
sh-5.2# java -cp "app.jar" $JAVA_OPTS io.github.tom0794.App
Listening for transport dt_socket at address: 5005
```

### Forward the port
In another terminal
```shell
kubectl port-forward <pod> -n tc 5005
```

### Run remote debugger config in intellij
Run -> Edit Configurations -> Add -> Remote JVM Debug. Defaults are fine