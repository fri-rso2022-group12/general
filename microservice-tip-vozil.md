# Microservice tip vozil

Mikrostoritev tip vozil je namenjena hrambi in delu s podatki o proizvajalcih in modelih vozil.

## Avtor

Martin Dagarin

## Tehnični podatki

Storitev je napisana v programskem jeziku [TypeScript](https://www.typescriptlang.org/) z uporabo ogrodja [NestJS](https://nestjs.com/), ki teče v okolju [NodeJS](https://nodejs.org/en/).   

URL za dostop:
- **API**: [`http://34.160.27.153/rso-vehicle-type`](http://34.160.27.153/rso-vehicle-type)
- **OpenAPI**: [`http://34.173.72.45:8080/openapi`](http://34.173.72.45:8080/openapi)
- **GraphQL**: [`http://34.173.72.45:8080/graphql`](http://34.173.72.45:8080/graphql)
- **gRPC**: `34.173.72.45:8081`

## Git repozitorij

https://github.com/fri-rso2022-group12/microservice-tip-vozil

## Docker Hub repozitorij

https://hub.docker.com/repository/docker/slocomptech/rso-microservice-tip-vozil

## Video

Tip vozil

[![Video](https://img.youtube.com/vi/YI0pyZSy4dY/0.jpg)](https://www.youtube.com/watch?v=YI0pyZSy4dY)
https://www.youtube.com/watch?v=YI0pyZSy4dY

## Zvezna integracija

S pomočjo **Github actions** je bila vzpostavljena zvezna integracija, ki poskrbi za novo sliko Docker in jo tudi objavi na [Dockerhub repozitoriju](https://hub.docker.com/repository/docker/slocomptech/rso-microservice-vozila). Sama konfiguracija je na voljo na [https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/.github/workflows/docker.yml](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/.github/workflows/docker.yml).

## Kubernetes

Mikrostoritev je bila objavljena na **Amazon Kuberenets**. Konfiguracija je definirana v dveh datotekah:

- [rso-microservice-tip-vozil-deployment.yaml](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/k8s/rso-microservice-tip-vozil-deployment.yaml)
- secrets.yaml, ki hrani zaupne podatke (ni v repozitoriju)

Podatkovna baza je začasno gostovana na lastnem strežniku.

## Upravljanje s konfiguracijo

Mikrostoritev podpira več različnih tipov konfiguracije in sicer:

- s **konfiguracijsko datoteko** (pot nastavimo s spremenljivko *CONFIG*)
- z [**okoljskimi spremenljivkami**](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/custom-config/config.schema.ts)
- v [**konfiguracijskem strežniku**](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/custom-config/consul-config.service.ts)

V *konfiguracijski datoteki* in *okoljskih spremenljivkah* so definirane splošne nastavitve, ki so vezane na posamezno instanco (dostop do baze, drugih storitev).

V *konfiguracijskem strežniku* je pa definirana spremenljivka *MAINTENANCE_MODE*, s katero onemogočimo dostop do vseh storitev mikrostoritve (razen healtcheckov). Implementacija se vidi [tukaj](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/maintenance.middleware.ts).


## Preverjanje vitalnosti

Za preverjanje vitalnosti mikrostoritev implementira sledeča endpointa:

- `/health/live`, ki pove, če se mikrostoritev izvaja
- `/health/ready`, ki pove, če je mikrostoritev pripravljena za obdelavo zahtevkov

Implementacija preverjanja vitalnosti, se nahaja [tukaj](https://github.com/fri-rso2022-group12/microservice-tip-vozil/tree/master/src/health).

Simuliranje bolne storitve izvedemo z POST zahtevkom na `/health/destroy`.

## Zbiranje metrik

Zbiranje metrik poteka s pomočjo Prometheusa. Mikrostoritev ima za ta namen izpostavljen endpoint `/metrics`. Dodana je bila tudi lastna metrika *request_served*, ki šteje prejete zahtevke. Šteje zahtevkov je narejeno s pomočjo [middleware-a](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/request-counter.interceptor.ts)


## Beleženje dnevnikov

Beleženje dnevnikov je narejno na ravni Kubernetes noda. S pomočjo [Filebeat](https://www.elastic.co/beats/filebeat) se dnevniški zapiši vseh vsebnikov posredujejo na Elastic. Loggiranje vstopov in izstopov iz posameznega endpointa je implementirano [tukaj](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/http-logging.interceptor.ts).

## Zunaji API

Ta mikrostoritev uporablja zunanji API [Car Data](https://rapidapi.com/principalapis/api/car-data/). Preko tega API mikrostoritev dobim več informacij o modelu nekega vozila npr. seznam vseh različnih izdelav modela vozila. Implementacija klica zunanjega API je na voljo [tukaj](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/model-vozila/model-vozila.service.ts#L37).

## Napredni komunikacijski protokoli

### Asinhron REST klic

Mikrostoritev kot asinhron REST klic vključuje [klic zunanjega API](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/model-vozila/model-vozila.service.ts#L43). Ker je API implementiran v NodeJS, se asinhronost izvede s pomočjo Promise. Klic medote, ki opravi asinhron REST klic, lahko sprožimo z GET metodo na `/model-vozila/:id/izdelave`.  

### GraphQL

Mikrostoritev ima implementiran tudi GraphQL endpoint, ki podpira sledeči metodi:

- `proizvajalec_getAll{id, naziv}` za pridobitev seznama vseh proizvajalecev vozil
- `proizvajalec_getById(id: <ID>){id, naziv}` za pridobitev podatkov o specificiranem proizvajalcu vozil

## gRPC

Z mikrostoritvijo lahko komuniciramo tudi preko protokola **gRPC**. Vmesnik je definiran s pomočjo [**protobufferja**](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/app.proto).

Izpostavljeni sta sledeči metodi:

- `getAll` za pridobitev seznama vseh proizvajalec vozil
- `getById` za pridobitev podatkov o specificiranem proizvajalcu vozil

## Kafka

Mikrostoritev uporablja tudi pretočno platformo Kafka. Ta mikrostoritev uporablja Kafko, da obvesti mikrostoritev Vozila o izbrisu modela vozila z namenom, da lahko ciljna mikrostoritev izbriše vozila tega modela. To je implementirano [tukaj](https://github.com/fri-rso2022-group12/microservice-tip-vozil/blob/master/src/model-vozila/model-vozila.service.ts#L71).
