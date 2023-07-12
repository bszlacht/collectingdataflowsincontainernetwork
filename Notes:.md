Notes:

---- Cel

Zebrać metryki takie podobne do tych które są w artykule 'Is advance knowledge of flow sizes
a plausible assumption?'.
Czyli:

- CPU usage
- Disk I/O
- Memoty I/O
- Network in
- Network out

---- Czym jest flow

1. Zbiór danych z repozytorium dołączonego do artykułu nie da się pobrać w związku z czym nie będziemy mogli się na nim wzorować.

2. Autorzy definiują flow na dwa sposoby:

- jako TCP 5 Tuple

> 5-Tuple: The tuple (source IP address, source port, destination IP address, destination port, transport protocol). A 5-tuple uniquely identifies a UDP/TCP session. When a UDP/TCP session flows through a NAT64, each session has two different 5-tuples: one with IPv4 addresses and one with IPv6 addresses.

- jako seria pakietów od A do B, które są wysyłane w krótkim czasie

Różnica między tymi sposobami polega na tym, że w pierwszym przypadku pakiet == flow, natomiast w drugim niekoniecznie.
W celach uproszczenia my będziemy stosować 1. definicję.

---- Metryki w Cilium które można wyeksportować do Prometeusza bez dodatkowych narzędzi

1. Wszystkie metryki które można zaciągnąć do Prometeusza z Hubble'a oraz Cilium są wymienione na stronie: <https://docs.cilium.io/en/stable/observability/metrics/>

2. Najbardziej zbliżone metryki do tych które były w artykule: <https://docs.cilium.io/en/stable/observability/metrics/#hubble-exported-metrics>. Możemy stąd wyciągnąć network in, network out (http_requests_total)

Te metryki jednak nie udostępniają informacji o obciążeniu procesora, Memory I/O oraz Disk I/O.

---- Inspektor Gadget

1. Konieczny sugerował użyć to narzędzie <https://github.com/inspektor-gadget/inspektor-gadget/blob/main/docs/gadgets/top/ebpf.md>

Wyzwania:

- wypisuje output na konsolę, który zawiera dużo kolumn
- output nie jest powiązany z requestami które istnieją

2. Inspektor gadget ma wtyczkę do prometeusza. Idealnie było by użyć jej wraz żeby monitorować gauge dla CPU usage.
Potem, można będzie powiązać tę metrykę z flowami, które zwraca hubble.
<https://github.com/inspektor-gadget/inspektor-gadget/blob/main/docs/gadgets/prometheus.md>

Nie możemy użyć tego z brzegu ponieważ:
> Error: rpc error: code = Unknown desc = running gadget: running gadget: gauge cpu_usage: only one-shot gadgets are supported

TODO:

1. Zformatować output, albo użyć jakąś alternatywę do ig, która pozwala na monitoring wykorzystania CPU do prometeusza
