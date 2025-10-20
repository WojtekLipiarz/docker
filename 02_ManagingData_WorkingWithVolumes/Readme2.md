## 1. Sposób działania aplikacji:

- apliakcja ma prosty formularz z polem name i opis
- po wysłaniu formularza dane zapisywane są w pliku temp
- następnie jezeli plik o tej samej nazwie (title) nie istnieje to przenoszone jest do folderu feedback

## 2. Praktyczne zastosowanie DOcker

Pliki które przechowywane są w w tymczasowych folderach (temp i feedback) nie są zapisywane w kodzie źródłowym aplikacji tylko w konenerze przypisanym dla danego image.
Zmiany są przechowywane w konenerze więc po usunięciu konenera (temp i feedback) zostanie wyczyszczone. Jednak te dane przechowywane są w ramach pamięci konenera. Więc jezeli konener tylko damy stop i uruchomimy go ponownie to wszystkie dane będą dalej dodstępne

## 3. Volumes - sposób na zachowanie danych po usunięciu konenera

// Voluems are folders on your host machine hard drive which are mounted into container

Ten zapis tworzy volume ale nie zapewnia mozlwiości aby automatycznie przypisać te dane do wskazanego konenera. Volume jest zapisane gdzieś na naszym komputerze ale bez wskazania specyficznego miejsca.

```
VOLUME [ "/app/feedback" ]
```

Ta komenda pozwala wyświetlić listę wszystkich istnijących volume

```
docker volume ls
```

Poniewaz ten przykład daje volume któ®e jest anonimus to po zastopowaniu konenera nie zostanie ona nigdzie zapisana

## 4. Aby zabezpieczyć usówanie volume po rm konenera nalerzy wykorzysgtać nazwane volume

W tym celu VOLUME [ "/app/feedback" ] w pliku Dockerfile nie jest potrzebny. Nalezy dodać volume z poziomu uruchamiania konenera:

```
-v feedback:/app/feedback
```

- -v to klucz inicjujący volume
- feedback: to nazwa tego volume
- /app/feedback to jego lokalizacja wewnątrz konenera (app poniewaz tak jest zadeklarowane jako WORKDIR)

Na przykład:

```
docker run -p 3000:80 -d --rm --name feedback-app -v feedback:/app/feedback feedback-node:la
```

## 5. rm anonymus volume

Jezeli kontener nie został uruchomiony z flagą --rm to volume nie usunię się automatycznie nawet jak zatrzymamy konener i go usuniemy.
Aby to zrobić nalezy uzyc jednej z tych komend

```
docker volume rm VOL_NAME
```

or

```
docker volume prune
```

## 6. Bind Mounts

W procesie developmentu generujemy zmiany w kodzie źródłowym. Aby mieć do niech dostęp kozystając z Docker nalezy za kazdym razem tworzyć nowy image.
Rozwiązaniem tutaj będzie wykorzystanie Bind Mounts

Za pomocą volume tworzymy powiązanie z lokalizacją naszego kodu

```
-v appPath:/app
```

np

```
docker run -p 3000:80 -d --rm --name feedback-app -v feedback:/app/feedback -v "/Users/wojciechlipiarz/Desktop/kursy/Docker/02_ManagingData_WorkingWithVolumes/data-volumes:/app" feedback-node:latest
```

pamiętaj o mozliwości uzycia shortcuts:
macOS / Linux: -v $(pwd):/app

Windows: -v "%cd%":/app

```
docker run -p 3000:80 -d --rm --name feedback-app -v feedback:/app/feedback -v $(pwd):/app feedback-node:latest
```

## 7. Problem z Bind Mounts

Wykorzystujac Bind Mounts cały folder jest dodawany ze wskazanego miejsca. W ten sposób zostało usunięte nodemoduls. Jednym z rozwiązań jest dodanie jeszcze jednego anonimowego volume który doda nam te nodemodules z poziomu app konenera

volume jest anonimowe poniewaz nie dodaliśmy nam: NAMOE:/xyz

```
docker run -p 3000:80 -d --rm --name feedback-app -v feedback:/app/feedback -v $(pwd):/app -v /app/node_modules feedback-node:latest
```

## 8. Problem z Bind Mounts i node

Zmiana dla samego serwera node nie zadziała bez konieczności ponownego uruchomienia konenera np. jezeli chcemy dodać do app.post('/create', async (req, res) lakiś log to nie pojawi się zmiana automatycznie. Problem rozwiązuje wykorzystanie nodemon.

## 9. Zabezpieczenie zeby docker conteiner podczas kozystania z Bind Mounts nie andpisywał plików

W tym celu wykorzystuje się flagę read only

# $(pwd):/app:ro

```
docker run -p 3000:80 -d --rm --name feedback-app -v feedback:/app/feedback -v $(pwd):/app:ro -v /app/node_modules feedback-node:latest
```

## 10. Dostarczenie danych z temp folder do konenera

```
$(pwd):/app:ro
```

```
docker run -p 3000:80 -d --rm --name feedback-app -v feedback:/app/feedback -v $(pwd):/app:ro -v /app/temp -v /app/node_modules feedback-node:latest
```
