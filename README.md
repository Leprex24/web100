# Laboratorium 4

## Instrukcja budowy obrazu

### Dockerfile

Plik znajduje się w katalogu `web100/`.

### Budowanie obrazu

```bash
docker build -t web100 .
```

### Uruchomienie kontenera

```bash
docker run -d -p 8080:80 web100
```

Strona dostępna pod:

```
http://localhost:8080
```

---

## Etap 2 wykorzystane dobre praktyki

* użycie && w celu połączenia polecen RUN
* zmniejszenie rozmiaru obrazu przez użycie `apt-get clean`
* zastosowanie `CMD` w formie exec tak jak zalecane w pliku pdf z laboratorium

---

## Etap 2 liczba warstw obrazu

Polecenie:

```bash
docker history web100
```

Liczba warstw: 11

---

## Zadanie lab3 lokalny rejestr Docker (wszystkie etapy tego zadanie wykonane za pomocą WSL2)

### Konfiguracja

* wygenerowanie certyfikatu self-signed poleceniem
```bash
openssl req -newkey rsa:4096 -nodes -sha256 -keyout domain.key -x509 -days 365 -out domain.crt
```

* uruchomienie registry na porcie 5000 z HTTPS
```bash
docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name registry \
  -v $(pwd):/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
```

* dodannie certyfikatu do Dockera
```bash
sudo mkdir -p /etc/docker/certs.d/localhost:5000
sudo cp domain.crt /etc/docker/certs.d/localhost:5000/ca.crt
```

---

### Użycie rejestru

#### Tagowanie obrazu

```bash
docker tag web100 localhost:5000/web100
```

#### Wysłanie obrazu

```bash
docker push localhost:5000/web100
```

#### Sprawdzenie zawartości

```bash
curl -k https://localhost:5000/v2/_catalog
```

#### Pobranie obrazu

```bash
docker pull localhost:5000/web100
```

### Potwierdzenie wykonania niektórych poleceń:

```bash
kacper@Kacper:/mnt/c/Studia/chmura/registry/certs$ docker push localhost:5000/web100
Using default tag: latest
The push refers to repository [localhost:5000/web100]
817807f3c64e: Pushed
564bc784bf4b: Pushed
5f3df4c5951a: Pushed
b9bb6714aa29: Pushed
latest: digest: sha256:1d73dc742765100b91e8afc473c4651619c10c97076fba5eaf16b5592bedf226 size: 855
```

```bash
kacper@Kacper:/mnt/c/Studia/chmura/registry/certs$ curl -k https://localhost:5000/v2/_catalog
{"repositories":["web100"]}
```

```bash
kacper@Kacper:/mnt/c/Studia/chmura/registry/certs$ docker rmi localhost:5000/web100
Untagged: localhost:5000/web100:latest
kacper@Kacper:/mnt/c/Studia/chmura/registry/certs$ docker pull localhost:5000/web100
Using default tag: latest
latest: Pulling from web100
Digest: sha256:1d73dc742765100b91e8afc473c4651619c10c97076fba5eaf16b5592bedf226
Status: Downloaded newer image for localhost:5000/web100:latest
localhost:5000/web100:latest
```

---

## DockerHub

https://hub.docker.com/r/Leprex24/pawcho-sawicki

nazwa tagu:
Leprex24/pawcho-sawicki:web100.v1.0.0

---

## Repozytorium GitHub

https://github.com/Leprex24/web100

