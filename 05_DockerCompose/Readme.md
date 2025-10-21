# 1. Aby uruchomić docker-compose wystarczy uyć tej komendy w lokalizacji z plikiem docker-compose.yaml

```
docker-compose up
```

# 2. Uruchamianie w środowisku datach

```
docker-compose up -d
```

## aby zatrzymać proces

```
docker-compose down
```

## aby usunąć dodatkowo volumes

```
docker-compose down -v
```

# 3. Wymusznie build

# nowy build i run

```
docker-compose up --build
```

# tylko build

```
docker-compose build
```
