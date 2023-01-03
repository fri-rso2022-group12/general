# Microservice opravljene poti
Mikrostoritev voznje je namenjenja hranjenju, pridobivanju in analiziranju opravljenih voženj.

## Avtor
Tibor Čuš

## Tehnični podatki
Storitev je napisana v programskem jeziku Java in Spring boot.

## Git repozitorij
https://github.com/fri-rso2022-group12/microservice-voznje

## Docker Hub repozitorij
https://hub.docker.com/repository/docker/cutibo29/trips

## Video
[![Video](https://www.youtube.com/watch?v=5gyGwfJ5eiQ/0.jpg)](https://www.youtube.com/watch?v=5gyGwfJ5eiQ)


## Zvezna integracija

S pomočjo **Github actions** je bila vzpostavljena zvezna integracija, ki poskrbi za novo sliko Docker in jo tudi objavi na [Dockerhub repozitoriju](https://hub.docker.com/repository/docker/cutibo29/trips). Sama konfiguracija je na voljo na [https://github.com/fri-rso2022-group12/mikroservice-polnilna-postaja/blob/master/k8/charging-station-deployment.yaml](https://github.com/fri-rso2022-group12/mikroservice-polnilna-postaja/blob/master/k8/charging-station-deployment.yaml).

## Kubernetes

Mikrostoritev je bila objavljena na **Google kubernetis engine-u**. Konfiguracija je definirana v datoteki:

Podatkovna baza je MS SQL Server, gostovana na Azure.

## Upravljanje s konfiguracijo

Mikrostoritev podpira več različnih tipov konfiguracije in sicer:

- s **konfiguracijsko datoteko** 
- z **okoljskimi spremenljivkami**
V *konfiguracijski datoteki* in *okoljskih spremenljivkah* so definirane splošne nastavitve, ki so vezane na posamezno instanco (dostop do baze, drugih storitev).


## Preverjanje vitalnosti

Za preverjanje vitalnosti mikrostoritev implementira sledeča endpointa:

- [/health/live](http://34.72.77.27:8080/actuator/health/liveness), ki pove, če se mikrostoritev izvaja
- [/health/ready](http://34.72.77.27:8080/actuator/health/readiness), ki pove, če je mikrostoritev pripravljena za obdelavo zahtevkov

Simuliranje zdravja storitev se izvede s:
 - /api/v1/mock/health/rediness/good
 - /api/v1/mock/health/rediness/bad
 - /api/v1/mock/health/liveliness/good
 - /api/v1/mock/health/liveliness/bad

## Zbiranje metrik

Zbiranje metrik poteka s pomočjo [Prometheusa](http://35.224.249.132:9090/). Mikrostoritev ima za ta namen izpostavljen endpoint [/actuator/prometheus](http://34.72.77.27:8080/actuator/prometheus). Dodana je bila tudi lastna metrika *ApiCallCounter*, ki šteje prejete zahtevke. Šteje zahtevkov je narejeno s pomočjo *io.micrometer.core.instrument.Counter* paketa.


## Beleženje dnevnikov

Beleženje dnevnikov je narejno na ravni Kubernetes noda. S pomočjo [Filebeat](https://www.elastic.co/beats/filebeat) se dnevniški zapiši vseh vsebnikov posredujejo na Elastic. Loggiranje vstopov in izstopov iz posameznega endpointa je implementirano s pomočjo **org.slf4j.Logger** in **org.slf4j.LoggerFactory** knjižnic. Logi so dostopni preko [kibane](http://34.28.150.187:5601/app/home#/).

## Zunaji API
Ta storitev ne uporablja zunanjih api-jev.

## Toleranca napak
Za toleranco napak, se uporablja Circuit Breaker, ki temelji na **io.github.resilience4j.circuitbreaker.CircuitBreaker** paketu. V aplikaciji definiramo, drsečo okno poljubne širine (v našem primeru 3). V primeru, da je 50% > klicov v oknu počasnih (trajajo več kot dve minuti) ali neuspešnih, se sproži circuit breaker mehanizem, kateri prepreči klice za poljubno konfigurirano časovno obdobje. Po tem je stikalo v napol odprti poziciji in ponovno spremlja promet in se glede na vrednosti odpre ali zapre.

## Napredni komunikacijski protokoli

### Asinhron REST klic

Mikrostoritev uporablja [asinhrone REST klice](https://github.com/fri-rso2022-group12/microservice-voznje/blob/master/src/main/java/com/rso/Trips/api/TripsAsync.java) za pridobivanje podatkov iz baze.   

### GraphQL
Storitev ne uporablja graphql

## Kafka
Mikrostoritev ne uporablja kafke. 
