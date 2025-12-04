# 🚀 Sportpajzs n8n Automatizálás

> **n8n workflow automatizálás sportbiztosítási cold outreach emailekhez**
> Magyar nyelvű dokumentáció | Teljes körű hibaelhárítás | Lépésről lépésre telepítés

[![Version](https://img.shields.io/badge/version-2.0.0-blue.svg)](https://github.com/w7-mgfcode/sportpajzs-n8n-automation/releases)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Language](https://img.shields.io/badge/nyelv-Magyar-red.svg)]()
[![n8n](https://img.shields.io/badge/n8n-compatible-orange.svg)](https://n8n.io)

---

## 📋 Tartalom

- [Mi ez a projekt?](#-mi-ez-a-projekt)
- [Funkciók](#-funkciók)
- [Gyors telepítés (5 lépés)](#-gyors-telepítés-5-lépés)
- [Részletes dokumentáció](#-részletes-dokumentáció)
- [Verzió történet](#-verzió-történet)
- [Támogatás](#-támogatás)
- [Licenc](#-licenc)

---

## 🎯 Mi ez a projekt?

Ez egy **továbbfejlesztett n8n workflow csomag** a Sportpajzs termék automatikus email alapú értékesítéséhez.

**Használati eset:**
Automatikusan küld bemutatkozó emaileket potenciális partnereknek (sportklubok, egyesületek) egy Google Sheet alapján, miközben nyomon követi a küldött emaileket és kezeli a hibákat.

**Kinek készült?**
- Biztosítási értékesítőknek
- Insurance advisoroknak
- Akik szeretnének automatizálni cold outreach kampányokat
- n8n felhasználóknak (kezdő szinttől)

---

## ✨ Funkciók

### 🆕 v2.0 Új funkciók

| Funkció | Leírás |
|---------|--------|
| ✅ **Email validálás** | Ellenőrzi az email formátumot küldés előtt |
| ✅ **Hibaértesítés** | Automatikus email alert ha valami elromlik |
| ✅ **Rate limiting** | Maximum 50 email per futás (Gmail védelem) |
| ✅ **Időbélyeg** | Pontos dátum+idő naplózás (2025-12-03 10:15:30) |
| ✅ **Végrehajtás logolás** | Naplózza hány emailt küldött |
| ✅ **Hibakezelés** | Try/Catch node-ok, nem áll le hiba esetén |
| ✅ **Duplikáció védelem** | Csak azoknak küld akiknek még nem küldött |

### 📊 Automatizálás részletek

- **Ütemezés:** Kedd, Szerda, Csütörtök | 10:00 AM (Budapest időzóna)
- **Forrás:** Google Sheets (kontaktok listája)
- **Küldés:** Gmail API
- **Limit:** Max 50 email/futás, max 500/nap (Gmail limit)
- **Nyelvezet:** Magyar email tartalom

---

## ⚡ Gyors telepítés (5 lépés)

### Előfeltételek

- ✅ n8n fiók (Cloud vagy Self-hosted)
- ✅ Google fiók (Gmail + Sheets)
- ✅ Sportpajzs Gmail credential beállítva n8n-ben

### Lépések

#### 1️⃣ **Letöltés**

**Opció A: GitHub Release (ajánlott)**
```bash
# Menj a Releases oldalra
https://github.com/w7-mgfcode/sportpajzs-n8n-automation/releases/latest

# Töltsd le: sportpajzs-n8n-v2.0.0.zip
```

**Opció B: Git clone**
```bash
git clone https://github.com/w7-mgfcode/sportpajzs-n8n-automation.git
cd sportpajzs-n8n-automation
```

#### 2️⃣ **Google Sheet létrehozása**

1. Nyiss új [Google Sheet](https://sheets.google.com)-et
2. Importáld: `templates/sheet-template.csv`
3. Másold ki a Sheet URL-t

#### 3️⃣ **n8n Workflow importálás**

1. n8n → **Import from File**
2. Importáld: `workflows/cold-outreach-enhanced.json`
3. Importáld: `workflows/error-notification.json` (opcionális)

#### 4️⃣ **Beállítás**

1. **"Read Contacts from Sheet" node:**
   - Állítsd be a Google Sheet URL-t
   - Credential: Google Sheets OAuth

2. **"Send Intro Email" node:**
   - Credential: Sportpajzs Gmail

3. **"Send Error Alert" node:**
   - Send To: saját email címed

#### 5️⃣ **Tesztelés & Aktiválás**

1. Adj hozzá 1 teszt kontaktot (saját emaileddel)
2. **Execute Workflow** (manuális futtatás)
3. Ellenőrizd: email megérkezett? ✅
4. Kapcsold **Active** = ON

🎉 **Kész! A workflow automatikusan fut Kedd/Szerda/Csütörtök 10:00-kor!**

---

## 📚 Részletes dokumentáció

| Dokumentum | Leírás | Link |
|------------|--------|------|
| 📖 **Telepítési útmutató** | Lépésről lépésre telepítés (73 oldal) | [TELEPITES.md](docs/hu/TELEPITES.md) |
| 🔧 **Hibaelhárítás** | 7 gyakori hiba + megoldások (45 oldal) | [HIBAELHARITAS.md](docs/hu/HIBAELHARITAS.md) |
| ❓ **GYIK** | Gyakran ismételt kérdések | [GYIK.md](docs/hu/GYIK.md) |

---

## 📁 Projekt struktúra

```
sportpajzs-n8n-automation/
├── workflows/
│   ├── cold-outreach-enhanced.json     → Fő automatizálás (14 node)
│   └── error-notification.json         → Hiba értesítő (4 node)
├── templates/
│   ├── sheet-template.csv              → Google Sheet sablon
│   └── example-data.csv                → Példa kontaktok
├── docs/
│   └── hu/
│       ├── TELEPITES.md                → Telepítési útmutató
│       ├── HIBAELHARITAS.md            → Hibaelhárítás
│       └── GYIK.md                     → GYIK
└── README.md                           → Ez a fájl
```

---

## 🎨 Workflow diagramok

### Cold Outreach Workflow (Fő automatizálás)

```
[Schedule Trigger]
    ↓
[Read Google Sheet] → [Error Handler]
    ↓
[Validate Emails] (✅ email formátum ellenőrzés)
    ↓
[Filter Unsent] (csak Küldve=üres)
    ↓
[Rate Limit] (max 50 email)
    ↓
[Send Email] → [Error Handler]
    ↓
[Format Timestamp]
    ↓
[Update Sheet] (Küldve = 2025-12-03 10:15:30)
    ↓
[Log Execution] (naplózás)
```

### Error Notification Workflow

```
[Webhook Trigger] → [Format Error HU] → [Send Alert Email]
```

---

## 🆚 v2.0 vs v1.0 összehasonlítás

| Funkció | v1.0 (régi) | v2.0 (új) ✅ |
|---------|-------------|--------------|
| Email validálás | ❌ | ✅ |
| Hibaértesítés | ❌ | ✅ |
| Rate limiting | ❌ | ✅ Max 50/futás |
| Időbélyeg | "küldve" szöveg | ✅ 2025-12-03 10:15:30 |
| Logolás | ❌ | ✅ |
| Hibakezelés | ❌ | ✅ Try/Catch |
| Sheet séma | Bonyolult (trailing spaces) | ✅ Egyszerű |

---

## 📦 Verzió történet

### [v2.0.0](https://github.com/w7-mgfcode/sportpajzs-n8n-automation/releases/tag/v2.0.0) - 2025-12-03

#### ✨ Új funkciók
- Email validálás hozzáadva
- Hiba értesítés workflow (email alerts)
- Rate limiting (max 50 email/futás)
- Időbélyeg naplózás
- Végrehajtás logolás
- Automatikus hibakezelés (Try/Catch)

#### 🔧 Javítások
- Új egyszerűsített Sheet séma
- Oszlopnevek trailing space-ek eltávolítva
- JavaScript filter kód optimalizálva

#### 📚 Dokumentáció
- 129 oldal magyar nyelvű dokumentáció
- Telepítési útmutató (TELEPITES.md)
- Hibaelhárítási útmutató (HIBAELHARITAS.md)
- GYIK (GYIK.md)

#### 🔄 Migrálás v1.0-ról
Lásd: [CHANGELOG.md](CHANGELOG.md)

---

## ❓ Gyakori kérdések

### Mikor fut a workflow automatikusan?

**Kedd, Szerda, Csütörtök** reggel **10:00 AM** (Budapest időzóna)

### Hány emailt küld egyszerre?

Maximum **50 email per futás**. Ha több unsent kontaktod van, a következő futáskor küldi a maradékot.

### Mit jelent ha a "Küldve" oszlop üres?

Az üres "Küldve" oszlop = még **nem küldött** emailt ennek a kontaktnak. A workflow csak ezeket dolgozza fel.

### Hogyan tudom újra küldeni egy kontaktnak?

Töröld ki a **"Küldve"** oszlop értékét (üres cellává tedd). A következő futáskor újra küld neki.

### Kaphatok értesítést ha hiba van?

**Igen!** Az **"error-notification.json"** workflow automatikusan küld neked emailt ha valami elromlik.

### További kérdések?

Lásd: [docs/hu/GYIK.md](docs/hu/GYIK.md)

---

## 🆘 Támogatás

### Ha elakadtál

1. **Nézd meg a dokumentációt:**
   - [Telepítési útmutató](docs/hu/TELEPITES.md)
   - [Hibaelhárítás](docs/hu/HIBAELHARITAS.md)

2. **Nyiss egy Issue-t:**
   - [GitHub Issues](https://github.com/w7-mgfcode/sportpajzs-n8n-automation/issues/new)
   - Írj le mindent részletesen
   - Csatolj screenshot-okat

3. **Email támogatás:**
   - czunidaniel9@gmail.com
   - Válaszidő: 24-48 óra

---

## 🤝 Közreműködés

Jelenleg ez egy privát projekt Dani számára. Ha szeretnél hozzájárulni:
- Nyiss egy Issue-t javaslatokkal
- Készíts Pull Request-et javításokkal
- Oszd meg a visszajelzéseidet

---

## 📜 Licenc

MIT License - Lásd: [LICENSE](LICENSE)

---

## 🙏 Köszönetnyilvánítás

- **n8n** - Workflow automation platform
- **Google** - Gmail & Sheets APIs
- **Dani** - Projekt ötlet és tesztelés

---

## 📞 Kapcsolat

**Projekt karbantartó:** [Your Name]
**Email:** your.email@example.com
**n8n Közösség:** [https://community.n8n.io](https://community.n8n.io)

---

## 🔗 Hasznos linkek

- [n8n Dokumentáció](https://docs.n8n.io)
- [Gmail API Limitek](https://support.google.com/mail/answer/22839)
- [Google Sheets API](https://developers.google.com/sheets/api)
- [Sportpajzs weboldal](https://sportpajzs.hu/)

---

<p align="center">
  <strong>⭐ Ha hasznos volt, adj egy csillagot a repónak! ⭐</strong>
</p>

<p align="center">
  Készítve ❤️-tel Magyarország számára
</p>
