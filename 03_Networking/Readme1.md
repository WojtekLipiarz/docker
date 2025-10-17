# 1. Wstęp

```
docker build -t favorites-node .
```

```
docker run --name favorites -d --rm -p 3000:3000 favorites-node
```

Na tym etapie konener nie zadziała poniwaz MongoDB nie jest częścią aplikacji (mongodb://localhost:27017/swfavorites)

MongoNetworkError: failed to connect to server [localhost:27017]

# 2. Komuinkacją między koneneram a lokalną maszyną (Mongodb)

- Nie mozna uzywać localhost (jest to lokalizacja naszeg fizycznego komputera)
- zastąp (mongodb://localhost:27017/swfavorites) localhost => host.docker.internal

```
host.docker.internal
```

# 3. Praca z Mongodb bez lokalnej wersji

- nalezy stowrzyć konener dla mongodb

```
docker run -d --name mongodb mongo
```

- teraz mamy działający konener z mongodb. Musimy go połączyć z naszym konenerem node. Najpierw musimy znaleźć odpowiednie dane wewnątrz konenera

```
docker container inspect mongodb
```

- Tam nalezy skopiować IPAddress i dodać go jako ściezka do mongodb w naszym kodzie

# 172.17.0.2

```
mongodb://172.17.0.2:27017/swfavorites
```

# 3. Creating Container Network - lepsze rozwiązanie tego problemu

```
docker run --network my_network ...
```

- Njepier naley stowrzyć netowrk:

```
docker network create favorite-net
```

- uruchamianie konenera mongo db:

```
docker run -d --name mongodb --network favorite-net mongo
```

- W kodzie nalezy zaktualizować ściezkę do mongodb tak aby odnosiła się do nazwy konenera.

```
mongodb://mongodb:27017/swfavorites
```

- uruchom konener z aplikacją node w utworzonym środowisku network:

```
docker run --name favorites -d --rm -p 3000:3000 --network favorite-net favorites-node
```
