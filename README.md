# Collecting data flow information from container network
The general idea behind this project is to collect data about flow of TCP inside kubernetes container. We will use Hubble (https://github.com/cilium/hubble) to monitor packets. Our idea for the moment is:
1. Create a VM with Ubuntu installed.
2. Install mikro k8s from snap.
3. Create a simple k8s cluster.
4. Deploy app into the cluster.
5. Check what and how can we measure the flow inside.
