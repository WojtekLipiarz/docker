Docker moze być wykorzystany do inicjowania komend lokalnie.
np. node init, ale bez zainstalowanego node na swoim komputerze
Mozna tego dokonać na kilka sposóbów:

# 1. Za pomocą image dla node

-it => konener musi być w trybie interaktywnym
-d => abyśmy mieli dostęp do konsoli

```
docker run -it -d node
```

## teraz uruchamiamy skrypt node ale poza kontenerem

-it => aby mieć pełmą kontrolę nad

```
docker exec -it NODE_CONTAINER_NAME npm init
```

# 2. Za pomocą image dla node (ale od razu przy inicjowaniu kontenrea)

```
docker run -it node npm init
```

# 3. Najwygodniejszym sposobem jest wykorzystanie docker-compose

Oto komendy które wykorzysta się do obsługi tego. Zwróć uwagę ze tutaj zostało wszystko skonfigurowane tak ze jest zadeklarowane w Dockerfile i docker-compose

```
docker-compose run --rm ${SERVICE_NAME} ${YOUR_COMMEND}
```

np.

```
docker-compose run --rm npm init
```

# Pamiętak aby dodać falge --rm zeby usunac konener po jego wykorzystaniu
