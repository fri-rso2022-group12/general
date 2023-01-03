# Microservice Pot
Mikrostoritev pot je namenjena vračanju podatkov o najkrajši poti od točke A do točke B.

## Avtor
Peter Timotej Avsec

## Tehnični podatki
Storitev je napisana v programskem jeziku Go. Uporabljen je zunanji API Google Maps API.
Dostopna je na javnem IP naslovu http://34.105.194.53/

## Git repozitorij
https://github.com/fri-rso2022-group12/microservice-pot

## Docker Hub repozitorij
https://hub.docker.com/r/tavsec/rso-microservice-pot


## Video
[![Video](https://img.youtube.com/vi/fur44c-bDO0/0.jpg)](https://www.youtube.com/watch?v=fur44c-bDO0)

## Zvezna integracija
Ob vsakem pushu na Git vejo master, se sproži GitHub akcija, ki z Docker konfiguracije zgradi novo verzijo Docker slike in jo objavi na Docker Hub repozitorij.

## Kubernetes
Med samostojnim razvojem je bila storitev gostovana preko Google Kubernetes Engine, končna verzija pa je gostovana na Azure Kubernetes. Kubernetes konfiguracija se nahaja na: https://github.com/fri-rso2022-group12/microservice-pot/blob/master/kubernetes/deployment.yml

## Upravljanje s konfiguracijo
Storitev za konfiguracijo sistemskih spremenljivk uporablja Kubernetes Secrets. Pravtako omogoča povezavo s Hashicorp Consul, kjer je storitev pozorna na spremenljivko MAINTENANCE_MODE, s katero lahko aplikacijo postavimo v vzdrževalni način.

## Preverjanje vitalnosti
Za preverjanje vitalnosti sem ustvaril Go paket in ga objavil tudi na Go packages repozitoriju: https://pkg.go.dev/github.com/tavsec/gin-healthcheck . Ta paket omogoča preverjanje vitalnosti strežnika ter MySQL.

## Zbiranje metrik
Za zbiranje metrik sem vzpostavil Prometheus exporter, ki na endpoint /metrics izpostavi Go metrike. Za export metrik sem uporabil sledeči middleware: https://github.com/zsais/go-gin-prometheus

## Zunaji API
Storitev uporablja zunanji API Google Maps, ki na podlagi vnešenih parametrov vrne najbolj optimalno pot med dvema destinacijama.

## Toleranca napak
V primetu napake strežnika, je uporabljen Go sistem recover, ki ponovno vzpostavi strežnik.
