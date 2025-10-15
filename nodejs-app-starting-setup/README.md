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

## 4. Optymalizacja budowania obrazu

**Cel:** skrócenie czasu budowy obrazu poprzez efektywne wykorzystanie cache warstw Dockera.

### 🧱 Zasada:

Umieszczaj instrukcje, które rzadko się zmieniają (np. instalacja zależności), jak najwyżej w Dockerfile.  
Dzięki temu Docker może wykorzystać cache przy kolejnych budowach.

### 💡 Przykład (Node.js):

```Dockerfile
# 1. Ustaw katalog roboczy
WORKDIR /app

# 2. Skopiuj pliki definiujące zależności
COPY package*.json ./app

# 3. Zainstaluj zależności
RUN npm install

# 4. Skopiuj resztę plików aplikacji
COPY . .

EXPOSE 80

CMD ["node", "server.js"]
```

### 🔍 Dlaczego to działa:

- Docker tworzy warstwy (layers) dla każdej instrukcji.
- Jeśli **`package.json`** i **`package-lock.json`** się nie zmienią, warstwa z `npm install` zostanie **zcache’owana**.
- Dzięki temu, przy ponownej budowie, zależności nie będą instalowane od zera — oszczędność czasu i zasobów.

### 🚀 Dodatkowe wskazówki:

- Używaj `COPY package*.json .` zamiast tylko `package.json` — wtedy obsłużysz też `package-lock.json` lub `npm-shrinkwrap.json`.
- Dla środowisk developerskich można użyć `npm ci` (szybsze, bardziej deterministyczne).
- W przypadku dużych projektów warto rozważyć **multi-stage build**, aby końcowy obraz był mniejszy.
