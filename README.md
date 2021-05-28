# Getting Started

### Install JDK
    apt install openjdk-16-jdk
    vi /etc/environment
    JAVA_HOME=/usr/lib/jvm/java-16-openjdk-amd64
    source /etc/environment

### Install k3s without traefik
    /usr/local/bin/k3s-uninstall.sh
    curl -sfL https://get.k3s.io | bash -s -  --write-kubeconfig-mode "0644" --disable=traefik

### Install prometheus using helm - also installs prometheus-pushgateway
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo add kube-state-metrics https://kubernetes.github.io/kube-state-metrics
    helm repo update
    helm install prometheus prometheus-community/prometheus

### Run port forwards to access prometheus server, alertmanager and pushgateway
    export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
    kubectl --namespace default port-forward $POD_NAME 9090 &
    export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
    kubectl --namespace default port-forward $POD_NAME 9093 &
    export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
    kubectl --namespace default port-forward $POD_NAME 9091 &

### Prometheus URLs 
* server [http://127.0.0.1:9090/]()
* alertmanager [http://127.0.0.1:9093/]()
* pushgateway [http://127.0.0.1:9091/]()

### Build image
    mvn spring-boot:build-image

# Outstanding work left
* Run app in k3s cluster
* Add larger variety of metrics
* helm install for app
* Include k3s cluster test in build