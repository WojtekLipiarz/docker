# 📘 Docker – Zarządzanie danymi (Volumes i Bind Mounts)

## 1. Jak działa aplikacja z przykładu

- Aplikacja zawiera prosty formularz z polami **name** i **description**.
- Po wysłaniu formularza dane zapisywane są w folderze `temp`.
- Jeśli plik o danej nazwie jeszcze nie istnieje, zostaje przeniesiony do folderu `feedback`.

---

## 2. Dane w kontenerze

- Dane (np. pliki w `temp` i `feedback`) są zapisywane **wewnątrz kontenera**, nie w kodzie źródłowym.
- Po **zatrzymaniu** kontenera dane pozostają dostępne, ponieważ nadal istnieje kontener.
- Po **usunięciu** kontenera dane znikają — ponieważ istniały tylko w jego przestrzeni plików.

> 💡 Wniosek: jeśli chcesz zachować dane po usunięciu kontenera, musisz użyć **Volume**.

---

## 3. Volumes – zachowanie danych poza kontenerem

**Volumes** to foldery na Twoim komputerze (host machine), które są „zamontowane” do kontenera.  
Umożliwiają trwałe przechowywanie danych niezależnie od cyklu życia kontenera.

### Tworzenie wolumenu w Dockerfile

```Dockerfile
VOLUME [ "/app/feedback" ]
```

To polecenie tworzy **anonimowy volume**.  
Dane są zapisywane poza kontenerem, ale Docker nie przypisuje im konkretnej nazwy ani lokalizacji, więc:

- po zatrzymaniu kontenera dane mogą nadal istnieć,
- ale są trudne do zidentyfikowania i ponownego wykorzystania.

### Wyświetlenie wszystkich wolumenów

```bash
docker volume ls
```

---

## 4. Rodzaje Volume

| Typ Volume           | Tworzenie                               | Cechy                                        | Trwałość danych                                                |
| -------------------- | --------------------------------------- | -------------------------------------------- | -------------------------------------------------------------- |
| **Anonymous Volume** | `VOLUME ["/app/data"]` w Dockerfile     | Tworzony automatycznie, bez nazwy            | Traci się przy usunięciu kontenera (chyba że nie użyto `--rm`) |
| **Named Volume**     | `-v mydata:/app/data` przy uruchomieniu | Można łatwo ponownie użyć między kontenerami | Dane pozostają po usunięciu kontenera                          |
| **Bind Mount**       | `-v /ścieżka/na/hoście:/app`            | Łączy folder z dysku hosta z kontenerem      | Dane istnieją na dysku hosta, idealne do developmentu          |

---

## 5. Named Volumes – przykład

Nie potrzebujemy już `VOLUME` w `Dockerfile`.  
Tworzymy nazwany wolumen podczas uruchamiania kontenera:

```bash
docker run -p 3000:80 -d --rm --name feedback-app \
-v feedback:/app/feedback \
feedback-node:latest
```

- `-v feedback:/app/feedback` – tworzy nazwany volume `feedback`
- `/app/feedback` – ścieżka wewnątrz kontenera, gdzie dane będą zapisywane

Dzięki temu dane w `feedback` **pozostaną**, nawet jeśli usuniemy kontener.

---

## 6. Usuwanie wolumenów

Jeśli kontener nie był uruchomiony z flagą `--rm`, to anonimowe wolumeny **nie usuną się automatycznie**.  
Aby je usunąć ręcznie:

```bash
docker volume rm VOLUME_NAME
```

Lub usunąć wszystkie nieużywane:

```bash
docker volume prune
```

---

## 7. Bind Mounts – praca w trybie deweloperskim

W trybie developmentu często zmieniamy kod aplikacji.  
Normalnie, żeby te zmiany były widoczne, musielibyśmy budować nowy image za każdym razem.  
Z pomocą przychodzą **Bind Mounts**, które pozwalają „podłączyć” nasz lokalny kod do kontenera.

Przykład:

```bash
docker run -p 3000:80 -d --rm --name feedback-app \
-v feedback:/app/feedback \
-v "$(pwd):/app" \
feedback-node:latest
```

- `$(pwd)` (Linux/Mac) lub `%cd%` (Windows) – odnosi się do bieżącego katalogu projektu.
- Dzięki temu zmiany w kodzie lokalnym są natychmiast widoczne w kontenerze.

---

## 8. Problem z Bind Mounts – brak `node_modules`

Podczas montowania całego folderu projektu (`/app`) z hosta do kontenera:

- lokalny folder **nadpisuje** folder `/app` w kontenerze,
- więc tracimy katalog `node_modules` (który istnieje tylko w kontenerze).

Rozwiązanie:
Dodaj jeszcze jeden **anonimowy volume**, który zachowa `node_modules` z kontenera:

```bash
docker run -p 3000:80 -d --rm --name feedback-app \
-v feedback:/app/feedback \
-v "$(pwd):/app" \
-v /app/node_modules \
feedback-node:latest
```

---

## 9. Problem z restartowaniem serwera Node

Zmiana kodu (np. dodanie `console.log()` w `app.post('/create')`) **nie odświeży** automatycznie serwera.  
Rozwiązanie: użyj **Nodemon**, który automatycznie restartuje serwer po wykryciu zmian.

---

## 🧭 Podsumowanie

| Typ danych        | Gdzie się zapisują                       | Przetrwają usunięcie kontenera | Przykład                    |
| ----------------- | ---------------------------------------- | ------------------------------ | --------------------------- |
| Dane w kontenerze | Wewnątrz kontenera                       | ❌ Nie                         | brak                        |
| Anonymous Volume  | Na dysku hosta (Docker zarządza ścieżką) | 🔸 Czasami                     | `VOLUME ["/app/data"]`      |
| Named Volume      | Na dysku hosta (łatwo identyfikowalne)   | ✅ Tak                         | `-v feedback:/app/feedback` |
| Bind Mount        | Na dysku hosta (nasz folder projektu)    | ✅ Tak                         | `-v $(pwd):/app`            |

---

## 10. Docker ignore

dodaj plik:

```
.dockerignore
```

Jest to niezbędne aby przypadkiem pliki zainstalowane lokalnie nie wpływały na wygenerowany image podczas komendy COPY . .

## 11. ARGuments & ENVironmaint Variables

| ARG                                                                         | ENV                                                     |
| --------------------------------------------------------------------------- | ------------------------------------------------------- |
| Available inside of Dockerfile, NOT accesable in CMD or any aplication code | Available inside of Dockerfile & inside aplication code |
| Seto on image build (docker build) via -build-arg                           | Set via ENV in Dockerfile or via --env on docekr run    |

np. proccess.env.PORT
Domyślnie mamy PORT 80 ale mozna zmienic to podczas uruchamiania kontenera:

```
docker run -p 3000:8000 --env PORT=8000 -d --rm --name feedback-app -v feedback:/app/feedback -v $(pwd):/app:ro -v /app/node_modules feedback-node:latest
```

## Mozna tez wykorzystać dane z pliku .env

```
--env-file ./.env
```
