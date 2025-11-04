## Załozeniem tego projektu jest przygotowanie "Utility Container" tak eby nie trzeba było przygotowywać pełnej konfiguracji lokalnie.

Konfiguracja zawiera następujące serwisy:

- server (nginx)
- php (official image + custome extensions)
- mysql
- composer
- artisian
- npm

# stowrzenie projektu laravel

Lista dostępnych szablonów composer jest tutaj: packagist.org
my wykorzystujemy laravel/laravel ale jest wiele innych.
Jeeli chcesz przetestować jak to działa mozesz usunąc całą zawartość folderu src

```
docker-compose run --rm composer create-project --prefer-dist laravel/laravel .
```

Pamietaj o aktualizacji env w src:

```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret
```

# start project

```
docker compose up -d server php mysql
```

or (because we have depends_on)

```
docker compose up -d server
```

# You must run the Artisan migration FIRST

-- the default configuration now uses flatstorage (sqlite), and now we need to migrate the data to our SQL database

```
docker compose run --rm artisan migrate
```

one more command to update the view's cache

```
docker compose run --rm artisan view:cache
```

and finally (new with Laravel 12), you MUST generate an encryption key

```
docker compose run --rm artisan key:generate
```

# You should now be able to view your project at http://localhost:8000
