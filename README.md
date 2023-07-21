# Prerequisites

1. Install Hubble CLI

```bash
export HUBBLE_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/hubble/master/stable.txt)
curl -LO "https://github.com/cilium/hubble/releases/download/$HUBBLE_VERSION/hubble-linux-amd64.tar.gz"
curl -LO "https://github.com/cilium/hubble/releases/download/$HUBBLE_VERSION/hubble-linux-amd64.tar.gz.sha256sum"
sha256sum --check hubble-linux-amd64.tar.gz.sha256sum
tar zxf hubble-linux-amd64.tar.gz
sudo mv hubble /usr/local/bin
```

2. Install inspektor gadget plugin for kubectl

```bash
kubectl krew install gadget
```

# Configuring cluster

1. Start minikube cluster with below configuration and mount an ebpf filesystem

```
minikube start --network-plugin=cni --memory=4096
minikube start --cni=cilium --memory=4096
minikube ssh -- sudo mount bpffs -t bpf /sys/fs/bpf
```

2. Deploy Prometheus for collecting network stats and Grafana for visualising them

```bash
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/v1.12/examples/kubernetes/addons/prometheus/monitoring-example.yaml
```

3. Add cilium with configured metrics (see [metrics page](https://docs.cilium.io/en/stable/observability/metrics/#hubble-exported-metrics) for available metrics)

```bash
helm repo add cilium https://helm.cilium.io/
helm install cilium cilium/cilium --version 1.12.11 \
--namespace kube-system \
--set prometheus.enabled=true \
--set operator.prometheus.enabled=true \
--set hubble.enabled=true \
--set hubble.metrics.enabled="{dns,drop,tcp,flow,port-distribution,icmp,http}"
```

4. Deply Hubble in order to observe networks flows

```bash
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/v1.9/install/kubernetes/quick-hubble-install.yaml
```

5. Deploy inspektor daget

```bash
kubectl gadget deploy
```

6. Deploy an application

```bash
kubectl apply -f online-boutique/release/kubernetes/manifests.yaml
```

# Observability

1. Observe network statistcs

```bash
kubectl -n cilium-monitoring port-forward service/grafana --address 0.0.0.0 --address :: 3000:3000
```

![image](https://github.com/bszlacht/collectingdataflowsincontainernetwork/assets/21079319/f66e7094-c9e9-4781-a4bd-7121595a2035)

2. Observe network flows

```bash
kubectl port-forward -n kube-system svc/hubble-ui --address 0.0.0.0 --address :: 12000:80
```

3. Expose network flows for export

```bash
kubectl port-forward -n kube-system svc/hubble-relay --address 0.0.0.0 --address :: 4245:80
```

![image](https://github.com/bszlacht/collectingdataflowsincontainernetwork/assets/21079319/f66e7094-c9e9-4781-a4bd-7121595a2035)

4. Export network flows (while still exposing port from previous step)

```bash
hubble observe -o json --all > flows.json
```

and later analyze exported flows with cli

```bash
cat flows.json | hubble observe
```

# Analyze cluster health

1. [Current CPU usage and performance of eBPF programs](https://github.com/inspektor-gadget/inspektor-gadget/blob/main/docs/gadgets/top/ebpf.md)

```bash
kubectl gadget top ebpf
```

2. [Containers generating the most block device input/output](https://github.com/inspektor-gadget/inspektor-gadget/blob/main/docs/gadgets/top/block-io.md)

```bash
kubectl gadget top ebpf
```

3. [Processes running in selected containers](https://github.com/inspektor-gadget/inspektor-gadget/blob/main/docs/gadgets/snapshot/process.md)

```bash
kubectl gadget snapshot process
```

# Alternatives

An alternative worth looking at is [Pixie](https://work.withpixie.ai/live), which also a tool build on top of eBPF that provides network observability and cluster health monitoring, but in one ecosystem.
