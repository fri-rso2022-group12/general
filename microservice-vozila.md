# Microservice Vozila

Mikrostoritev vozila je namenjena hrambi in delu s podatki o posameznih vozilih.

## Avtor

Martin Dagarin

## Tehnični podatki

Storitev je napisana v programskem jeziku [TypeScript](https://www.typescriptlang.org/) z uporabo ogrodja [NestJS](https://nestjs.com/), ki teče v okolju [NodeJS](https://nodejs.org/en/).

URL za dostop:

- **API**: [`http://34.160.27.153/rso-vehicle`](http://34.160.27.153/rso-vehicle)
- **OpenAPI**: [`http://34.122.33.166:8081/openapi`](http://34.122.33.166:8081/openapi)

Dostopna je na javnem IP naslovu http://20.241.133.147:8080/  
OpenAPI specifikacija je dostopna na http://20.241.133.147:8080/openapi

## Git repozitorij

https://github.com/fri-rso2022-group12/microservice-vozila

## Docker Hub repozitorij

https://hub.docker.com/repository/docker/slocomptech/rso-microservice-vozila

## Video

[![Video](https://img.youtube.com/vi/LbiYaRT8C2g/0.jpg)](https://www.youtube.com/watch?v=LbiYaRT8C2g)
https://www.youtube.com/watch?v=LbiYaRT8C2g

## Zvezna integracija

S pomočjo **Github actions** je bila vzpostavljena zvezna integracija, ki poskrbi za novo sliko Docker in jo tudi objavi na [Dockerhub repozitoriju](https://hub.docker.com/repository/docker/slocomptech/rso-microservice-vozila). Sama konfiguracija je na voljo na [https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/.github/workflows/docker.yml](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/.github/workflows/docker.yml).

## Kubernetes

Mikrostoritev je bila objavljena na **Amazon Kuberenets**. Konfiguracija je definirana v dveh datotekah:

- [rso-microservice-tip-vozil-deployment.yaml](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/k8s/rso-microservice-vozila-deployment.yaml)
- secrets.yaml, ki hrani zaupne podatke (ni v repozitoriju)

Podatkovna baza je začasno gostovana na lastnem strežniku.

## Upravljanje s konfiguracijo

Mikrostoritev podpira več različnih tipov konfiguracije in sicer:

- s **konfiguracijsko datoteko** (pot nastavimo s spremenljivko *CONFIG*)
- z [**okoljskimi spremenljivkami**](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/src/custom-config/config.schema.ts)
- v [**konfiguracijskem strežniku**](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/src/custom-config/consul-config.service.ts)

V *konfiguracijski datoteki* in *okoljskih spremenljivkah* so definirane splošne nastavitve, ki so vezane na posamezno instanco (dostop do baze, drugih storitev).

V *konfiguracijskem strežniku* je pa definirana spremenljivka *MAINTENANCE_MODE*, s katero onemogočimo dostop do vseh storitev mikrostoritve (razen healtcheckov). Implementacija se vidi [tukaj](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/src/maintenance.middleware.ts).


## Preverjanje vitalnosti

Za preverjanje vitalnosti mikrostoritev implementira sledeča endpointa:

- `/health/live`, ki pove, če se mikrostoritev izvaja
- `/health/ready`, ki pove, če je mikrostoritev pripravljena za obdelavo zahtevkov

Implementacija preverjanja vitalnosti, se nahaja [tukaj](https://github.com/fri-rso2022-group12/microservice-vozila/tree/master/src/health).

Simuliranje bolne storitve izvedemo z POST zahtevkom na `/health/destroy`.

## Zbiranje metrik

Zbiranje metrik poteka s pomočjo Prometheusa. Mikrostoritev ima za ta namen izpostavljen endpoint `/metrics`. Dodana je bila tudi lastna metrika *request_served*, ki šteje prejete zahtevke. Šteje zahtevkov je narejeno s pomočjo [middleware-a](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/src/request-counter.interceptor.ts)


## Beleženje dnevnikov

Beleženje dnevnikov je narejno na ravni Kubernetes noda. S pomočjo [Filebeat](https://www.elastic.co/beats/filebeat) se dnevniški zapiši vseh vsebnikov posredujejo na Elastic. Loggiranje vstopov in izstopov iz posameznega endpointa je implementirano [tukaj](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/src/http-logging.interceptor.ts).

## Zunaji API

Ta mikrostoritev ne uporablja zunanjega API.

## Toleranca napak

Ta mikrostoritev ima tudi implementiran primer izolacije in tolerance napak. Uprablja se **prekinjevalec toka**, ki uporablja mehanizma *fallback* in *timeout*. Implementacija se nahaja [tukaj](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/src/app.controller.ts#L37).

## Napredni komunikacijski protokoli

### Asinhron REST klic

Mikrostoritev kot asinhron REST klic vključuje [klic zunanjega API](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/src/app.controller.ts#L37). Ker je API implementiran v NodeJS, se asinhronost izvede s pomočjo Promise. Klic medote, ki opravi asinhron REST klic, lahko sprožimo z GET metodo na `/test`.  

### GraphQL

Ta mikrostoritev ne implementira GraphQL.

## gRPC

Ta mikrostoritev ne implementira gRPC.

## Kafka

Mikrostoritev uporablja tudi pretočno platformo Kafka. Ta mikrostoritev uporablja Kafko, da sprejema obvestila od mikrostoritve Tip Vozil o izbrisu modela vozila z namenom, da lahko izbriše vozila tega modela. To je implementirano [tukaj](https://github.com/fri-rso2022-group12/microservice-vozila/blob/master/src/vozilo/vozilo.controller.ts#L81).
