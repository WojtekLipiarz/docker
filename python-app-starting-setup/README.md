## 1. Przydatne komendy

| komenda                | opis                                |
| ---------------------- | ----------------------------------- |
| docker ps -a           | Lista wszystkich konenerów          |
| docker ps              | Lista aktywnych konenerów           |
| docker rm X Y Z        | pozwala usunąć wskazany konener     |
| docker container prune | usówa wszystkie nieaktywne konenery |
| docker images          | Wyświtla listę images               |
| docker rmi X Y Z       | Pozwala usunąć wybrane image        |
| docker image prune     | Usówa wszsytkie image               |

## 🚀 run

| komenda                 | opis                                   |
| ----------------------- | -------------------------------------- |
| docker run -p 3000:80 X | Uruchamia kontener we wskazanym porcie |
| docker run -d X         | Uruchamia w trybie --detach            |
| docker run --rm X       | Usówa konener po jego wyłączeniu       |

## 🚀 image

| komenda                | opis                                               |
| ---------------------- | -------------------------------------------------- |
| docker image inspect X | Pozwala dowiedzieć się czegoś więcej o danym image |

## 🚀 cp

Pozwala kopiować pliki do istniejącego kontenera lub z istniejącego kontenera

| komenda                                 | opis                                                                                |
| --------------------------------------- | ----------------------------------------------------------------------------------- |
| docker cp dummy/. focused_hermann:/test | Kopiuje wszystkie pliki z folderu dummy do konenera focused_hermann w folderze test |
| docker cp focused_hermann:/test dummy   | Kopiuje wszystkie pliki z folderu test wewnątrz konenera test do folderu dummy      |

## 2. Naming & Tagging

## 🚀 container

Mozna zmienić name dla aktywnego konenera

```dockerfile
docker run -p 3000:80 -d --rm --name goalsapp e48f5b976ce3
```

## 🚀 images

Wewnątrz image standard jset utrzymania nomenklatury name : tag

| klucz | opis                                                         |
| ----- | ------------------------------------------------------------ |
| name  | Definiuje grupę bardziej specyficznych images np. "reponame" |
| tag   | Definiuje specyficzny image w grupie np. "14"                |

Przykład:

```dockerfile
docker build --tag goals:latest .
```

Dzięki temu mozemy uruchomić konkretny image z konkretną wersją

```dockerfile
docker run -p 3000:80 -d --rm --name goalsapp goals:latest
```
