# 🐳 Różnica między `docker run` a `docker start`

## 1. `docker run`

Polecenie `docker run` **tworzy i uruchamia nowy kontener** na podstawie obrazu (image).

### 🔧 Działanie:

- Tworzy nowy kontener z danego obrazu (`docker create`)
- Następnie go uruchamia (`docker start`)
- Opcjonalnie łączy się z jego terminalem (`docker attach`)

### 📘 Składnia:

```bash
docker run [opcje] <image> [komenda]
```

### 🧩 Przykłady:

```bash
docker run ubuntu echo "Hello Docker!"
docker run -it ubuntu /bin/bash
docker run -d nginx
```

---

## 2. `docker start`

Polecenie `docker start` **uruchamia istniejący (zatrzymany) kontener** — nie tworzy nowego.

### 🔧 Działanie:

- Wznawia kontener, który wcześniej został utworzony (`docker run` lub `docker create`)
- Nie tworzy nowej instancji
- Domyślnie **uruchamia kontener w tle**

### 📘 Składnia:

```bash
docker start [opcje] <container>
```

### 🧩 Przykłady:

```bash
docker start my_container
docker start -a -i my_container
```

---

## ⚙️ Tryby uruchamiania

### 🔹 `-d` — **Detached mode**

- Uruchamia kontener w tle (nie blokuje terminala)
- Użyteczne do uruchamiania usług (np. nginx, redis)
- Terminal nie jest połączony z procesem kontenera

```bash
docker run -d nginx
```

💡 Możesz później zobaczyć logi:

```bash
docker logs <container_id>
```

---

### 🔹 `-a` / `--attach` — **Attached mode**

- Terminal **łączy się** z procesem kontenera (stdout, stderr)
- Kontener działa **w trybie interaktywnym**
- Przydatne do obserwowania procesu lub interakcji z aplikacją CLI

```bash
docker start -a my_container
```

---

### 🔹 `-i` — **Interactive mode**

- Utrzymuje **otwarte STDIN** (można wpisywać polecenia)
- Często używane z `-t` (pseudo-terminal)

```bash
docker run -it ubuntu /bin/bash
```

---

## 🧠 Podsumowanie

| Cecha / Tryb                               | `docker run` | `docker start` |
| ------------------------------------------ | ------------ | -------------- |
| Tworzy nowy kontener                       | ✅ Tak       | ❌ Nie         |
| Uruchamia istniejący                       | ❌ Nie       | ✅ Tak         |
| Można użyć `-d` (detach)                   | ✅ Tak       | ✅ Tak         |
| Można użyć `-a` (attach)                   | ✅ Tak       | ✅ Tak         |
| Używany przy pierwszym uruchomieniu obrazu | ✅           | ❌             |
| Wymaga istniejącego kontenera              | ❌           | ✅             |
