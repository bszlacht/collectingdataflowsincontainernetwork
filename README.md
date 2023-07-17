![image](https://github.com/bszlacht/collectingdataflowsincontainernetwork/assets/21079319/47fca728-2b43-498a-998c-e25109fada4d)# Collecting data flow information from container network

The general idea behind this project is to collect data about flow of TCP inside kubernetes container. We will use [Hubble](https://github.com/cilium/hubble) to monitor packets. Our idea for the moment is:

1. Create a VM with Ubuntu installed.
2. Install mikro k8s from snap.
3. Create a simple k8s cluster.
4. Deploy app into the cluster based on the [Cilium Star Wars Demo](https://github.com/cilium/star-wars-demo).
5. Use Hubble CLI to measure flow in [cluster](https://docs.cilium.io/en/stable/gettingstarted/hubble_cli/#hubble-cli).

# Hubble UI

Exporting flows to file:

1. Install and download Hubble Cli

> export HUBBLE_VERSION=$(curl -s <https://raw.githubusercontent.com/cilium/hubble/master/stable.txt>)
> curl -LO "<https://github.com/cilium/hubble/releases/download/$HUBBLE_VERSION/hubble-linux-amd64.tar.gz>"
> curl -LO "<https://github.com/cilium/hubble/releases/download/$HUBBLE_VERSION/hubble-linux-amd64.tar.gz.sha256sum>"
> sha256sum --check hubble-linux-amd64.tar.gz.sha256sum
> tar zxf hubble-linux-amd64.tar.gz
> sudo mv hubble /usr/local/bin

2. Run Hubble Realy

> kubectl port-forward -n kube-system svc/hubble-relay --address 0.0.0.0 --address :: 4245:80

3. Export environment variable pointing to hubble relay port

> export HUBBLE_SERVER=localhost:4245

4. Collect flows by running

> hubble observe -o json --all > FLOWS_DESTINATION.json

4. Analyse flows from file with Hubble UI:

> cat flows.json | hubble observe

Example flows view
![image](https://github.com/bszlacht/collectingdataflowsincontainernetwork/assets/21079319/fac52482-00a4-434b-bafa-76f96ee6e89d)

# How to run

To start minikube with cilium and online botique app:

```
minikube start --network-plugin=cni --memory=4096
minikube ssh -- sudo mount bpffs -t bpf /sys/fs/bpf
kubectl apply -f socks/release/kubernetes/manifests.yaml

kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/v1.9/install/kubernetes/quick-install.yaml
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/v1.9/install/kubernetes/quick-hubble-install.yaml
```

To open Hubble UI add redirect 12000 -> 80

```
kubectl port-forward -n kube-system svc/hubble-ui --address 0.0.0.0 --address :: 12000:80
```

and open localhost:12000 in your browser
You will be presented with smth similar to
![image](https://github.com/bszlacht/collectingdataflowsincontainernetwork/assets/21079319/289f7477-3105-4f23-b66b-bb0d95874ab3)

To open online boituqe by itself:

```
kubectl port-forward -n default deployment/frontend-v1 8080:8080
```

and open localhost:8080. You will see smth like that
![image](https://github.com/bszlacht/collectingdataflowsincontainernetwork/assets/21079319/f66e7094-c9e9-4781-a4bd-7121595a2035)
