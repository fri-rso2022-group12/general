# Microservice Uporabniki
Mikrostoritev uporabniki je namenjena manipulaciji entitet uporabnikov (kreacija, seznam, izbris).

## Avtor
Peter Timotej Avsec

## Tehnični podatki
Storitev je napisana v programskem jeziku Go.  
Dostopna je na javnem IP naslovu http://34.160.27.153/users-service

## Git repozitorij
https://github.com/fri-rso2022-group12/microservice-uporabniki

## Docker Hub repozitorij
https://hub.docker.com/r/tavsec/rso-microservice-uporabniki

## Video
[![Video](https://img.youtube.com/vi/OnF0uRr2IwI/0.jpg)](https://www.youtube.com/watch?v=OnF0uRr2IwI)
https://www.youtube.com/watch?v=OnF0uRr2IwI

## Zvezna integracija
Ob vsakem pushu na Git vejo master, se sproži GitHub akcija, ki z Docker konfiguracije zgradi novo verzijo Docker slike in jo objavi na Docker Hub repozitorij.

## Kubernetes
Med samostojnim razvojem je bila storitev gostovana preko Google Kubernetes Engine, končna verzija pa je gostovana na Azure Kubernetes. Kubernetes konfiguracija se nahaja na: https://github.com/fri-rso2022-group12/microservice-uporabniki/blob/master/kubernetes/deployment.yml

## Upravljanje s konfiguracijo
Storitev za konfiguracijo sistemskih spremenljivk uporablja Kubernetes Secrets. Pravtako omogoča povezavo s Hashicorp Consul, kjer je storitev pozorna na spremenljivko MAINTENANCE_MODE, s katero lahko aplikacijo postavimo v vzdrževalni način.

## Preverjanje vitalnosti
Za preverjanje vitalnosti sem ustvaril Go paket in ga objavil tudi na Go packages repozitoriju: https://pkg.go.dev/github.com/tavsec/gin-healthcheck . Ta paket omogoča preverjanje vitalnosti strežnika ter MySQL.

## Zbiranje metrik
Za zbiranje metrik sem vzpostavil Prometheus exporter, ki na endpoint /metrics izpostavi Go metrike. Za export metrik sem uporabil sledeči middleware: https://github.com/zsais/go-gin-prometheus

## Zunaji API
Ta storitev ne uporablja zunanjih API-jev.

## Toleranca napak
V primetu napake strežnika, je uporabljen Go sistem recover, ki ponovno vzpostavi strežnik.

