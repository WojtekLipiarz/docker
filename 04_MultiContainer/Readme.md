# 1. Wstęp

Ten projekt wymaga mongodb do działania (zarówno lokalnie jak i w środowisku docker)

# 2. Bez network

# Backend

// Pamiętaj ze mongodb mousi odnosić się do host.docker.internal

- Storzenie image dla bakend

```
docker build -t goal-node .
```

- uruchamianie konenera mongo db:
  // port jest wymagany (27017:27017) bo nie mamy networku

```
docker run -d --rm -p 27017:27017 --name mongodb mongo
```

- uruchom konener z aplikacją node:

```
docker run --name goal-backend -d --rm -p 80:80 goal-node
```

# Frontend

- Storzenie image dla frontendu

```
docker build -t goal-react .
```

- uruchom konener z aplikacji React:

```
docker run --name goal-frontend -d --rm -p 3000:3000 goal-react
```

// W starych wersjach wymagane było dodanie falgi -it

# 3. Z network

# Global

- Stworzenie network

```
docker network create goal-net
```

# Backend

// Pamiętaj ze mongodb mousi odnosić się do MONGODB_NAME (z konenera)

- Storzenie image dla bakend

```
docker build -t goal-node .
```

- uruchamianie konenera mongo db:
  // Teraz port (-p 27017:27017) nie jest potrzebny bo mamy network

```
docker run -d --rm --name mongodb --network goal-net mongo
```

- uruchom konener z aplikacją node:
  // Port (-p 80:80) JEST potrzebny aby działał poprawnie FE i postman

```
docker run --name goal-backend -d --rm -p 80:80 --network goal-net goal-node
```

# Frontend

// Fronend nie potrzebuje połączenia z network. Bazuje on na lokalhost i nie musi komunikaować się między konenerami

- Storzenie image dla frontendu

```
docker build -t goal-react .
```

- uruchom konener z aplikacją React:

```
docker run --name goal-frontend -d --rm -p 3000:3000 goal-react
```

// W starych wersjach wymagane było dodanie falgi -it

# 4. Zapisywanie danych mongodb tak aby działały równiez poz zmaknięciu kontenera

```
docker run -d --rm --name mongodb -v data:/data/db --network goal-net mongo
```

# 5. mongodb - dodanie warstwy autoryzacyjnej

// pamiętaj ze volume moze mien wplyw na autoryzacje wiec bedzie trzeba usunąc

```
docker run -d --rm --name mongodb -v data:/data/db -e MONGO_INITDB_ROOT_USERNAME=max -e MONGO_INITDB_ROOT_PASSWORD=secret --network goal-net mongo
```

Aby to działało trzeba zaktualizować mongoose.connect

```
mongodb://root:secret@mongodb:27017/course-goals?authSource=admin
```

## Mozna tez wykorzystać env:

```
ENV MONGODB_USERNAME root
ENV MONGODB_PASSWORD secret
```

```
`mongodb://${process.env.MONGODB_USERNAME}:${process.env.MONGODB_PASSWORD}@mongodb:27017/course-goals?authSource=admin`
```

# 6. backend - zapisywanie logów i aktualizacja konenera pod zmiany lokalne

```
docker run --name goal-backend -v $(pwd):/app -v logs:/app/logs -v /app/node_modules -e MONGODB_USERNAME=max --rm -d  -p 80:80 --network goal-net goal-node
```

# 7 bind frontend

```
docker run --name goal-frontend -v $(pwd):/app/src -d --rm -p 3000:3000 goal-react
```

# 8 Podsumowanie

NETWORK

```
docker network create goal-net
```

MONGODB

```
docker run -d --rm --name mongodb -v data:/data/db -e MONGO_INITDB_ROOT_USERNAME=max -e MONGO_INITDB_ROOT_PASSWORD=secret --network goal-net mongo
```

BACKEND (z poziomu folderu backend)

```
docker build -t goal-node .
```

```
docker run --name goal-backend -v $(pwd):/app -v logs:/app/logs -v /app/node_modules -e MONGODB_USERNAME=max --rm -d -p 80:80 --network goal-net goal-node
```

FRONTEND (z poziomu folderu frontend)

```
docker build -t goal-react .
```

```
docker run --name goal-frontend -v $(pwd)/src:/app/src -d --rm -p 3000:3000 goal-react
```
