# 🐳 Docker — Notatka: RUN vs CMD

## 1. CMD — co to jest?

Instrukcja **`CMD`** w pliku **Dockerfile** określa **polecenie, które uruchamia się automatycznie, gdy kontener startuje**.  
To coś jak „domyślne polecenie”, które wykona się po uruchomieniu kontenera.

Przykład:

```dockerfile
CMD ["node", "server.js"]
```

Oznacza:

> Uruchom Node.js i wykonaj plik `server.js`, kiedy kontener się uruchomi.

---

## 2. CMD vs RUN — jaka jest różnica?

Często początkujący mylą instrukcję **`CMD`** z **`RUN`**, ale one działają w zupełnie innym momencie życia obrazu.

### 🧱 `RUN`

- Używane **podczas budowania obrazu**.
- Wykonuje polecenie na etapie tworzenia obrazu Dockera.
- Zmiany zostają **zapisane w warstwie obrazu**.
- **Nie działa przy uruchamianiu kontenera** — więc nie uruchomi Twojej aplikacji po `docker run`.

Przykład:

```dockerfile
RUN npm install
```

➡️ Zainstaluje zależności podczas budowania obrazu.

---

### 🚀 `CMD`

- Używane **podczas uruchamiania kontenera** (czyli po zbudowaniu obrazu).
- Określa, **co ma się uruchomić automatycznie**, gdy kontener startuje.
- Może być nadpisane przy uruchamianiu kontenera, np.:

```bash
docker run myapp node test.js
```

Przykład:

```dockerfile
CMD ["node", "server.js"]
```

➡️ Uruchomi serwer dopiero, gdy kontener wystartuje.

---

## 3. Krótkie porównanie

| Instrukcja | Kiedy działa                       | Co robi                                                | Czy uruchamia aplikację? |
| ---------- | ---------------------------------- | ------------------------------------------------------ | ------------------------ |
| `RUN`      | podczas **budowania obrazu**       | tworzy warstwy obrazu (instalacje, kopie plików, itp.) | ❌ Nie                   |
| `CMD`      | podczas **uruchamiania kontenera** | uruchamia domyślny proces                              | ✅ Tak                   |

---
