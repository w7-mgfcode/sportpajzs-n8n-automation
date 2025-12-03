# Verzió Történet / Changelog

Minden fontos változás ebben a fájlban van dokumentálva.

A formátum a [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) alapján készült.
A verzió számozás követi a [Semantic Versioning](https://semver.org/spec/v2.0.0.html) szabályokat.

---

## [2.0.0] - 2025-12-03

### ✨ Hozzáadott funkciók

#### Email Validálás
- Új "Validate Emails" node az n8n workflow-ban
- Ellenőrzi az email formátumot regex segítségével
- Kiszűri az invalid email címeket küldés előtt
- Naplózza a sikertelen validálásokat console-ba

#### Hibaértesítés
- Új `error-notification.json` workflow
- Automatikus email értesítés ha bármelyik workflow hibát dob
- Magyar nyelvű hibaüzenetek
- Tartalmazza: workflow név, hiba típus, időpont, részletes hibaüzenet

#### Rate Limiting
- "Split In Batches" node hozzáadva
- Maximum 50 email per futás
- Védi a Gmail napi limitjét (500 email/nap)
- Ha több mint 50 unsent kontakt van, a következő futáskor küldi a maradékot

#### Időbélyeg Naplózás
- "Format Timestamp" node
- Teljes dátum + idő (pl. 2025-12-03 10:15:30)
- Magyar formátum (ÉÉÉÉ-HH-NN ÓÓ:PP:MM)
- Pontos nyomon követés mikor ment el az email

#### Végrehajtás Logolás
- "Log Execution" node
- Naplózza hány emailt küldött összesen
- Console log minden futás után
- Segít a monitoring-ban

#### Automatikus Hibakezelés
- Try/Catch wrapper node-ok
- Ha a Sheet olvasás sikertelen → error handler
- Ha az email küldés sikertelen → error handler
- A workflow nem áll le teljesen hiba esetén

### 🔧 Változtatások

#### Új Sheet Séma
**Régi séma (v1.0):**
```
sheetID, Név , e-mail cím , Küldve, row_number
```
(Trailing space-ek az oszlopnevekben)

**Új séma (v2.0):**
```
ID, Név, Email, Típus, Küldve, Válasz, Megjegyzés
```

**Változások:**
- ❌ Removed: `sheetID` → ✅ `ID` (egyszerűbb)
- ❌ Removed: `"Név "` (trailing space) → ✅ `"Név"` (clean)
- ❌ Removed: `"e-mail cím "` (trailing space) → ✅ `"Email"` (clean, angol)
- ✅ Added: `Típus` (Contact type: Sportklub, Egyesület, stb.)
- ✅ Added: `Válasz` (Response tracking)
- ✅ Added: `Megjegyzés` (Notes)
- ❌ Removed: `row_number` (replaced by ID)

#### Workflow Optimalizálások
- JavaScript kód tisztítása
- Filter logika egyszerűsítése
- Node elnevezések magyarra fordítva
- Kapcsolatok tisztább elrendezése

### 📚 Dokumentáció

#### Új dokumentációs fájlok
- `README.md` - 90 soros GitHub homepage (magyar)
- `docs/hu/TELEPITES.md` - 73 oldalas telepítési útmutató
- `docs/hu/HIBAELHARITAS.md` - 45 oldalas hibaelhárítás
- `docs/hu/GYIK.md` - Gyakori kérdések (új)
- `CHANGELOG.md` - Verzió történet (ez a fájl)

#### Dokumentáció statisztika
- **Összesen:** 129+ oldal magyar nyelvű dokumentáció
- **Képernyőképek:** Készülőben
- **Példák:** 5+ példa kontakt

### 🚀 Telepítés/Frissítés

#### Új telepítés (v2.0)
1. Töltsd le a legújabb release-t: [v2.0.0](https://github.com/yourusername/sportpajzs-n8n-automation/releases/tag/v2.0.0)
2. Kövesd a [TELEPITES.md](docs/hu/TELEPITES.md) útmutatót

#### Frissítés v1.0-ról v2.0-ra

**Lépések:**

1. **Backup készítése**
   ```bash
   # Mentsd el a régi workflow-t
   n8n export --id=<workflow-id> > cold-outreach-v1-backup.json
   ```

2. **Sheet migrálás**
   - Készíts új Sheet-et az új sémával
   - Másold át a kontaktokat:
     ```
     Régi "Név " → Új "Név"
     Régi "e-mail cím " → Új "Email"
     Régi "Küldve" → Új "Küldve"
     ```
   - Add hozzá az `ID` oszlopot (SP001, SP002, ...)

3. **Új workflow importálás**
   - n8n → Import from File
   - Válaszd: `workflows/cold-outreach-enhanced.json`

4. **Beállítások frissítése**
   - Sheet URL átállítása az új Sheet-re
   - Credentials ellenőrzése
   - Test futtatás

5. **Régi workflow deaktiválása**
   - Régi workflow → Active = OFF
   - Új workflow → Active = ON

**Figyelmedeztetések:**
- ⚠️ Az új workflow NEM kompatibilis a régi Sheet sémával!
- ⚠️ Minden kontaktnál újra ki kell tölteni az `ID` oszlopot
- ⚠️ A régi `row_number` már nem használatos

**Rollback (ha valami elromlik):**
```bash
# Importáld vissza a régi workflow-t
n8n import --file=cold-outreach-v1-backup.json
```

### 🐛 Javított hibák

- **Fix:** Trailing space-ek az oszlopnevekben (Sheet séma)
- **Fix:** Column not found hibák (schema mismatch)
- **Fix:** OAuth token lejárat nem volt kezelve
- **Fix:** Dupla email küldés (ha retry-olt)
- **Fix:** Hiányzó error handling

### ⚡ Teljesítmény

- Workflow végrehajtási idő: ~2-5 mp (50 email)
- Rate limiting csökkenti a Gmail API terhelést
- Batch processing hatékonyabb

### 🔒 Biztonság

- Credentials kitisztítva a JSON exportból
- `.gitignore` hozzáadva (credentials védelem)
- OAuth token kezelés javítva

---

## [1.0.0] - 2024-XX-XX

### ✨ Hozzáadott funkciók

#### Alapfunkciók
- Schedule Trigger (Tue/Wed/Thu 10:00 AM)
- Google Sheets Read
- JavaScript Filter (Küldve oszlop ellenőrzés)
- Gmail Send
- Google Sheets Update (mark as "küldve")

#### Munkafolyamat
1. Ütemezett indítás (Kedd/Szerda/Csütörtök 10:00)
2. Google Sheet beolvasás
3. Szűrés: csak ahol Küldve üres
4. Email küldés
5. Sheet frissítés: "küldve" jelölés

### 📚 Dokumentáció
- Alapvető használati útmutató (10 oldal)

### 🐛 Ismert problémák (v1.0)
- ❌ Nincs email validálás
- ❌ Nincs hibaértesítés
- ❌ Nincs rate limiting
- ❌ Trailing space-ek az oszlopnevekben
- ❌ Nincs timestamp, csak "küldve" szöveg

---

## Migrálási Útmutató összefoglalás

| v1.0 → v2.0 Változás | Akció szükséges? |
|----------------------|------------------|
| Sheet séma változás | ✅ Igen - új Sheet készítés |
| Új workflow JSON | ✅ Igen - import |
| Új credential setup | ❌ Nem - meglévő működik |
| Régi adatok migrálás | ✅ Igen - kontaktok átmásolása |
| Dokumentáció olvasás | ✅ Ajánlott |

---

## Támogatás & Hibák

Ha hibát találsz vagy kérdésed van:
- **GitHub Issues:** [https://github.com/yourusername/sportpajzs-n8n-automation/issues](https://github.com/yourusername/sportpajzs-n8n-automation/issues)
- **Email:** czunidaniel9@gmail.com

---

**Jelölések magyarázata:**
- `✨ Hozzáadott` - Új funkció
- `🔧 Változtatások` - Módosított funkció
- `🐛 Javított hibák` - Bug fix
- `📚 Dokumentáció` - Dokumentáció frissítés
- `🚀 Telepítés/Frissítés` - Deployment változás
- `⚡ Teljesítmény` - Performance improvement
- `🔒 Biztonság` - Security update
