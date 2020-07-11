# Applicazioni Cloud Native: Oracle in azione - Demo 2

## Agenda

1. Overview del sistema
2. CNA tools
3. K8S horizontal scaling
4. CI/CD via Wercker

## 1. Overview del sistema

Utilizzando la shell della macchina *bastion* con accesso pubblico si visualizzano le caratteristiche del sistema:
```bash
kubectl get no
```
```bash
kubectl get no -L node-type
```
Sui nodi monitoring-node=true sono installati i tool:
```bash
kubectl get namespace
```

In ogni namespace si trova una applicazione specifica.

Ad esempio Grafana / Prometheus sono nel namespace = monitoring:
```bash
kubectl get pod -n monitoring
```
Stesso risultato si puo vedere in maniera piu agevole anche tramite k9s:
```bash
~/k9s
```

## 2. CNA tools

Si mostra i seguenti tool:

* Ranger + Ranger dashboard
* Grafana / Prometheus
* Kibana / ElasticSearch
* KubeView
* k9s

## 3. K8S horizontal scaling

### Sample application overview

Sample application per lo stress test:

![image-20200711153929561](image/image-20200711153929561.png)

Per vedere i componenti deployati nel cluster che sottintendano la sample application si possono usare i seguenti comandi:
```bash
kubectl get deployment,pod,hpa,services,endpoints -n main-app -owide
```
```bash
kubectl get deployment,pod,hpa,services,endpoints -n core-services -owide
```

### Monitoring

Per monitorare il sistema durante il carico si può usare il seguente comando (da lanciare su altra shell rispetto a quella in cui si lancia il 'siege'):

```bash
watch -n 1 "kubectl top no && kubectl get hpa,po -n core-services"
```
```bash
~/k9s
```

Oppure i seguenti tool:

* Rancher
* KubeView
* Grafana
  * Dashboard Cluster
  * Dashboard Nodes
* Kibana
  * Mostrare le varie "Search"

### Stress del sistema

Si lancia il carico sul sistema con il seguente comando:


```bash
siege -c 10 -r 10000 "http://130.61.206.200/LoadOKE/TestOKEService?servlist=email-service.core-services.svc.cluster.local:8080,pdf-generation-service.core-services.svc.cluster.local:8080,digitalsignchecker-service.core-services.svc.cluster.local:8080&threadnum=5,5,5&elabtime=100,100,100&errperc=10,5,10"
```

Dopo alcuni minuti di carico il sistema raggiunge il regime (massimi valori impostati nel hpa i.e. 10 pod per deployment).

