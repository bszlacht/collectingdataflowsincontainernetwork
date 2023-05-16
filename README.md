# Collecting data flow information from container network
The general idea behind this project is to collect data about flow of TCP inside kubernetes container. We will use [Hubble](https://github.com/cilium/hubble) to monitor packets. Our idea for the moment is:
1. Create a VM with Ubuntu installed.
2. Install mikro k8s from snap.
3. Create a simple k8s cluster.
4. Deploy app into the cluster based on the [Cilium Star Wars Demo](https://github.com/cilium/star-wars-demo).
5. Use Hubble CLI to measure flow in [cluster](https://docs.cilium.io/en/stable/gettingstarted/hubble_cli/#hubble-cli).

Pytania i odpowiedzi:

1. Czy nie ma problemu z użyciem star-wars-demo udostępnionym przez Cilium?

Idziemy w stronę star-wars-demo.

2. Po co mielibyśmy stawić VM z Ubuntu i jakie będzie jej zadanie?

Nie musimy. Możemy jeśli chcemy nie zaśmiecać własny system.

3. > all gathered data should be exported to text format
Czy chodzi o to, że powinniśmy dowiedzieć się jak konkretnie to robi Hubble?

Chcemy opracować to jak zbierać dane o przeplywach do plików tekstowych. To jest główny cel projektu

4. Co jest efektem końcowym projektu i jak wygląda oddawanie: czy przekazujemy VMkę, czy po prostu raport?

Przekazujemy raport oraz dataset. Raport powinien opisywać jak postawić taki projekt do monitoringu na własnym klustrze

5. > find open data sets with flow data
Do czego powinniśmy tego użyć?
Czy może być taki:
<https://data.4tu.nl/articles/dataset/AssureMOSS_Kubernetes_Run-time_Monitoring_Dataset/20463687>

Możemy się na tym wzorować, aczkolwiek powinien być bardziej wyskopoziomowy: zawierać id podów, oraz liczbę pakietóœ, a nie byte'ów

6. Jak często się spotykamy?
Powinno być conajmniej 3 konsultacji. Na razie już była 1. Na następnych powinniśmy przedstawić częściowo działający projekt.
