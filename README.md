# Collecting data flow information from container network

The general idea behind this project is to collect data about flow of TCP inside kubernetes container. We will use [Hubble](https://github.com/cilium/hubble) to monitor packets. Our idea for the moment is:

1. Create a VM with Ubuntu installed.
2. Install mikro k8s from snap.
3. Create a simple k8s cluster.
4. Deploy app into the cluster based on the [Cilium Star Wars Demo](https://github.com/cilium/star-wars-demo).
5. Use Hubble CLI to measure flow in [cluster](https://docs.cilium.io/en/stable/gettingstarted/hubble_cli/#hubble-cli).

# Hubble UI

Exporting flows to file:
> hubble observe -o jsonpb --last 1000 > flows.json

Analyse flows from file with Hubble UI:
> cat flows.json | hubble observe

Example flows view
![image](https://github.com/bszlacht/collectingdataflowsincontainernetwork/assets/21079319/fac52482-00a4-434b-bafa-76f96ee6e89d)

# How to run

To start minikube with cilium and online botique app:

```
minikube start --network-plugin=cni --memory=4096
minikube start --cni=cilium --memory=4096
kubectl apply -f socks/release/kubernetes/manifests.yaml
```

After that add redirect 12000 -> 80

```
kubectl port-forward -n kube-system svc/hubble-ui --address 0.0.0.0 --address :: 12000:80
```

Open localhost:12000 in your browser
