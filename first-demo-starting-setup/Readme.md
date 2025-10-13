## 1. Przydatne komendy

| Branch                           | Description                                                                                      |
| :------------------------------- | :----------------------------------------------------------------------------------------------- |
| `docker build .`                 | Buduje kontenerer dla wskazanego pliku                                                           |
| `docker start -p 3000:3000 [id]` | Po zakończonym buildzie mamy dostęp do id. Możemy teraz uruchomić ten build we wskazanym porcie. |
| `docker ps`                      | Wyświetla listę uruchomionych procesów                                                           |
| `docker stop [name]`             | Zatrzymuje wskazany proces                                                                       |
