# Sportbiztosítás Email Automatizálás - Használati Útmutató

**Verzió:** 2.0 (Továbbfejlesztett)
**Dátum:** 2025-12-03
**Nyelv:** Magyar

---

## 📋 Tartalomjegyzék

1. [Bevezetés](#-bevezetés)
2. [Mi változott?](#-mi-változott)
3. [Telepítés lépésről lépésre](#-telepítés-lépésről-lépésre)
4. [Használat](#-használat)
5. [Gyakori kérdések (GYIK)](#-gyakori-kérdések-gyik)
6. [Támogatás](#-támogatás)

---

## 🎯 Bevezetés

### Mi ez a rendszer?

Ez egy továbbfejlesztett n8n workflow, amely automatikusan küld bemutatkozó emaileket a Sportpajzs termékről potenciális partnereknek (sportklubok, egyesületek, stb.).

### Mit csinál?

- **Automatikus email küldés**: Kedden, szerdán és csütörtökön reggel 10:00-kor
- **Duplikáció védelem**: Csak azoknak küld, akiknek még nem küldött
- **Hibajelzés**: Ha valami elromlik, emailben értesít
- **Validálás**: Ellenőrzi az email címeket küldés előtt
- **Limit védelem**: Maximum 50 emailt küld egyszerre (Gmail limit védelem)
- **Időbélyeg**: Pontosan naplózza mikor küldött emailt

---

## 🆕 Mi változott?

### Új funkciók a régi verzióhoz képest:

| Funkció | Régi | Új ✅ |
|---------|------|------|
| Email validálás | ❌ | ✅ Ellenőrzi a formátumot |
| Hibaértesítés | ❌ | ✅ Email alert ha hiba van |
| Rate limiting | ❌ | ✅ Max 50 email/futás |
| Időbélyeg | Csak "küldve" | ✅ Pontos dátum+idő |
| Logolás | ❌ | ✅ Naplózza hány emailt küldött |
| Hibakezelés | ❌ | ✅ Automatikus hiba detektálás |

### Új Google Sheet séma:

**RÉGI oszlopok:**
- sheetID
- Név  (felesleges szóközzel)
- e-mail cím  (felesleges szóközzel)
- Küldve
- row_number

**ÚJ oszlopok (egyszerűbb, tisztább):**
- **ID** - Egyedi azonosító (pl. SP001)
- **Név** - Kapcsolat neve
- **Email** - Email cím (NINCS szóköz!)
- **Típus** - Milyen típusú (Sportklub, Egyesület, stb.)
- **Küldve** - Küldés időpontja (2025-12-03 10:00:15)
- **Válasz** - Válaszolt-e? (opcionális)
- **Megjegyzés** - Jegyzetek (opcionális)

---

## 🚀 Telepítés lépésről lépésre

### LÉPÉS 1: Google Sheet létrehozása

#### 1.1. Új Google Sheet készítése

1. Menj a [Google Sheets](https://sheets.google.com)-re
2. Klikk: **+ Üres táblázat**
3. Nevezd el: **"Sportpajzs Megkeresések"**

#### 1.2. Sablon importálása

**OPCIÓ A: CSV importálás (ajánlott)**

1. A mellékelt `sportbiztositas-template.csv` fájlt nyisd meg
2. Google Sheets-ben: **Fájl → Importálás**
3. Válaszd ki a CSV fájlt
4. Importálási beállítások:
   - Import helye: **"Meglévő táblázat cseréje"**
   - Elválasztó karakter: **Vessző**
   - Szöveg konvertálása: **Igen**
5. Klikk: **Adatok importálása**

**OPCIÓ B: Kézi létrehozás**

Másold be pontosan ezeket az oszlopneveket az első sorba:

```
ID | Név | Email | Típus | Küldve | Válasz | Megjegyzés
```

⚠️ **FONTOS:**
- Pontosan ezek az oszlopnevek (ékezetek fontosak!)
- NINCS felesleges szóköz az oszlopnevek után!
- Az első sor legyen az oszlopnevek (header)

#### 1.3. Példa kontaktok hozzáadása (teszteléshez)

Használd a mellékelt `example-data.csv` fájlt, vagy add hozzá kézzel:

| ID | Név | Email | Típus | Küldve | Válasz | Megjegyzés |
|----|-----|-------|-------|--------|--------|------------|
| SP001 | Teszt Sportklub | tesztemail@saját-email.hu | Teszt | | | Teszthez használd a saját emailedet! |

⚠️ **TIPP:** Az első kontakt legyen a saját email címed, így tesztelheted hogy működik-e!

#### 1.4. Sheet URL másolása

1. A Google Sheet URL-t másold ki a böngésző címsorából
2. Teljes URL kell, pl:
   ```
   https://docs.google.com/spreadsheets/d/14IxPi-dSkmkNJdDdOutMx8oct8m0v0Ywt_pfsALyyz8/edit?gid=0#gid=0
   ```
3. Mentsd el ezt az URL-t, később szükséged lesz rá!

---

### LÉPÉS 2: n8n Workflow importálása

#### 2.1. Cold Outreach workflow importálás

1. Nyisd meg az n8n-t (https://saját-n8n-instance.com)
2. Bal felső sarokban klikk: **☰ Menü**
3. Válaszd: **Workflows**
4. Jobb felül klikk: **+ Add Workflow**
5. Jobb felső sarokban **≡** (három vonal) ikon
6. Válaszd: **Import from File**
7. Válaszd ki: `cold-outreach-enhanced.json`
8. Klikk: **Import**

✅ **Eredmény:** Új workflow jelenik meg: **"Sportbiztosítás Cold Outreach - ENHANCED"**

#### 2.2. Error Notification workflow importálás (opcionális, de ajánlott!)

Ismételd meg ugyanazokat a lépéseket ezzel a fájllal:
- `error-notification.json`

Ez a workflow értesít emailben, ha valami elromlik.

---

### LÉPÉS 3: Credential beállítások ellenőrzése

#### 3.1. Gmail credential

1. n8n-ben menj: **Credentials** (bal oldali menü)
2. Keresd meg: **"Sportpajzs Gmail"** (vagy a Gmail credential neved)
3. Klikk rá → Ellenőrizd:
   - **Status:** ✅ Connected
   - Ha **❌ Disconnected** vagy **⚠️ Expired**:
     - Klikk: **Reauthorize** vagy **Reconnect**
     - Jelentkezz be újra Googlenál
     - Hagyd jóvá az engedélyeket

#### 3.2. Google Sheets credential

1. n8n-ben menj: **Credentials**
2. Keresd meg: **"Google Sheets account"**
3. Ellenőrizd ugyanúgy mint a Gmail-nél
4. Ha kell: **Reauthorize**

⚠️ **Ha nincs ilyen credential-ed:** Létre kell hozni!
- n8n → Credentials → **+ Add Credential**
- Válaszd: **Google Sheets OAuth2 API**
- Kövesd az n8n utasításait a Google OAuth beállításához

---

### LÉPÉS 4: Sheet ID beállítása a workflow-ban

#### 4.1. Workflow megnyitása

1. n8n → Workflows
2. Klikk: **"Sportbiztosítás Cold Outreach - ENHANCED"**

#### 4.2. Google Sheets node-ok beállítása

**"Read Contacts from Sheet" node:**

1. Klikk a node-ra (zöld Google Sheets ikon)
2. **Document** mező:
   - Válaszd: **From URL**
   - Illeszd be a Google Sheet URL-t (amit LÉPÉS 1.4-ben másoltál)
3. **Sheet Name:** Válaszd ki: **"Sportpajzs megkeresések"** (vagy a sheet neve)
4. **Credential:** Válaszd ki a **Google Sheets account** credential-t

**"Mark as Sent" node:**

1. Klikk a node-ra (másik Google Sheets node, lentebb)
2. Állítsd be ugyanazt a **Document URL-t**
3. Ugyanaz a **Sheet Name** és **Credential**

#### 4.3. Gmail node beállítása

**"Send Intro Email" node:**

1. Klikk a node-ra (piros Gmail ikon)
2. **Credential:** Válaszd: **Sportpajzs Gmail**
3. **To Email:** Ne változtass rajta (automatikusan veszi a Sheet-ből)
4. **Subject és Message:** Már be van állítva (változtathatod ha akarod)

#### 4.4. Error Alert node beállítása

**"Send Error Alert to Dani" node:**

1. Klikk a node-ra (legalul)
2. **Send To:** Állítsd be a saját email címedet! (pl. `czunidaniel9@gmail.com`)
3. **Credential:** A személyes Gmail fiókod

---

### LÉPÉS 5: Tesztelés

#### 5.1. Teszt kontakt létrehozása

1. Google Sheet-ben adj hozzá egy sort a saját email címeddel:
   ```
   SP999 | Teszt Dani | czunidaniel9@gmail.com | Teszt | [ÜRE] | | Teszt futás
   ```
2. **Fontos:** A **Küldve** oszlop legyen üres!

#### 5.2. Manuális futtatás

1. n8n workflow-ban, jobb felül:
2. Klikk: **Execute Workflow** (Lejátszás gomb)
3. Várd meg amíg lefut (zöld pipák jelennek meg)

#### 5.3. Ellenőrzés

✅ **Sikeres teszt jelei:**

1. Az n8n minden node **zöld pipás** ✅
2. **Email megérkezett** a saját fiókodba
   - Subject: "Sportpajzs – rövid bemutatkozás"
   - Tartalom: bemutatkozó email
3. **Google Sheet frissült**:
   - A **Küldve** oszlopban megjelent az időpont (pl. "2025-12-03 10:15:30")

❌ **Ha valami nem működik:**

1. Nézd meg az n8n-ben melyik node piros ❌
2. Klikk rá a piros node-ra → olvasd el a hibaüzenetet
3. Lásd: [TROUBLESHOOT-HU.md](./TROUBLESHOOT-HU.md) a megoldásokért

---

## 📖 Használat

### Kontaktok hozzáadása

1. Nyisd meg a Google Sheet-et
2. Adj hozzá új sort az utolsó sor alá:
   - **ID:** Egyedi azonosító (pl. SP010, SP011, ...)
   - **Név:** Sportklub vagy egyesület neve
   - **Email:** Kapcsolattartó email címe
   - **Típus:** Milyen típusú (Sportklub, Egyesület, Vállalat, stb.)
   - **Küldve, Válasz, Megjegyzés:** Hagyd üresen!

3. **Mentsd el** a Google Sheet-et (automatikus mentés)

### Automatikus futás

A workflow **automatikusan fut**:
- **Napok:** Kedd, Szerda, Csütörtök
- **Időpont:** 10:00 AM (Budapest időzóna)

**Mit csinál:**
1. Beolvassa a Sheet-et
2. Szűri: csak azokat akiknek a **Küldve** oszlop üres
3. Validálja az email címeket
4. Küld maximum 50 emailt (rate limiting)
5. Frissíti a **Küldve** oszlopot időbélyeggel

### Végrehajtások ellenőrzése

1. n8n → **Executions** (bal oldali menü)
2. Látod az összes futás listáját:
   - ✅ Zöld = Sikeres
   - ❌ Piros = Hiba történt
3. Klikkelj egy futásra → részletek:
   - Hány email küldött
   - Mely node-ok futottak le
   - Ha hiba volt: mi volt a hibaüzenet

---

## ❓ Gyakori kérdések (GYIK)

### Mikor fut a workflow automatikusan?

**Válasz:** Hetente 3 alkalommal:
- **Kedd** reggel 10:00
- **Szerda** reggel 10:00
- **Csütörtök** reggel 10:00

**Időzóna:** Europe/Budapest (magyar idő)

### Hány emailt küld egyszerre?

**Válasz:** Maximum **50 email per futás**.

**Miért?** Ez védi a Gmail limit-et (500 email/nap). Ha 50-nél több unsent kontaktod van, a következő futáskor küldi a maradékot.

**Példa számítás:**
- Hétfőn hozzáadsz 120 új kontaktot
- Kedden: 50 email ✅
- Szerdán: 50 email ✅
- Csütörtökön: 20 email ✅
- Összesen: mind a 120 email elküldve 3 nap alatt

### Mit jelent ha a "Küldve" oszlop üres?

**Válasz:** Az üres "Küldve" oszlop azt jelenti, hogy még **NEM küldött emailt** ennek a kontaktnak.

A workflow csak azokat a sorokat dolgozza fel, ahol a **Küldve** oszlop üres.

### Hogyan tudom újra küldeni egy kontaktnak?

Ha valaki nem válaszolt és szeretnéd újra megkeresni:

1. Nyisd meg a Google Sheet-et
2. Keresd meg a kontaktot
3. **Töröld ki** a **Küldve** oszlop értékét (üres cellává tedd)
4. Mentsd el
5. A következő futáskor újra küld neki emailt

⚠️ **Figyelem:** Használd ezt óvatosan, ne spam-elj!

### Mennyi az email küldés limitje?

**Gmail Personal (ingyenes):** 500 email/nap
**Google Workspace (fizetős):** 2000 email/nap

A workflow **rate limiting** funkciója védi, hogy ne lépd túl:
- Maximum 50 email/futás
- Hetente 3 futás = 150 email/hét
- Ez **biztonságosan** a limit alatt van

### Honnan tudom hogy email-t küldött?

**3 hely ellenőrizd:**

1. **Google Sheet:**
   - A **Küldve** oszlopban megjelenik az időpont

2. **n8n Executions:**
   - n8n → Executions → legutóbbi futás ✅ zöld
   - "Log Execution" node → látod hány email-t küldött

3. **Gmail Sent (Elküldött):**
   - Sportpajzs Gmail fiók → Sent folder
   - Látod az elküldött email-eket

### Kaphatok értesítést ha hiba van?

**Igen!** A **"Send Error Alert to Dani"** node automatikusan küld neked emailt ha:
- OAuth credential lejár
- Sheet nem olvasható
- Email küldés sikertelen
- Bármilyen más hiba történik

**Email formátum:**
```
⚠️ n8n WORKFLOW HIBA ÉRTESÍTÉS

HIBA TÍPUS: OAuth / Hitelesítési hiba
Workflow: Cold Outreach
Időpont: 2025-12-03 10:15:30

HIBA ÜZENET:
[részletes hibaüzenet]

JAVASOLT MEGOLDÁS:
[lépések a hiba megoldásához]
```

### Hogyan tudom megváltoztatni az email szövegét?

1. n8n → "Sportbiztosítás Cold Outreach - ENHANCED" workflow
2. Klikk: **"Send Intro Email"** node
3. **Message** mező: szerkeszd a HTML tartalmat
4. **Tipp:** Őrizd meg a `{{ $json.Név }}` placeholder-t (ez behelyettesíti a nevet)
5. **Save** a workflow-t

### Hol vannak a képek az emailben?

A **Sportpajzs pecsét** kép az emailben egy Google Drive linkről töltődik be:
```
https://lh3.googleusercontent.com/d/1ZKugX-lOqelGhnlByHNwx51H11BcnuGM=s150
```

**Ha szeretnéd megváltoztatni:**
1. Töltsd fel a saját képedet Google Drive-ra
2. Állítsd be: **Bárki megtekintheti a linkkel**
3. Másold ki a link-et
4. n8n workflow → "Send Intro Email" node → Message → cseréld ki az URL-t

---

## 🆘 Támogatás

### Ha elakadtál

1. **Hibaelhárítási útmutató:**
   - Olvasd el: [TROUBLESHOOT-HU.md](./TROUBLESHOOT-HU.md)
   - Itt megtalálod a leggyakoribb hibákat és megoldásokat

2. **n8n Executions log:**
   - n8n → Executions
   - Klikkelj a hibás futásra
   - Nézd meg melyik node-nál állt le
   - Olvasd el a hibaüzenetet

3. **Screenshot készítés:**
   - Ha nem tudod megoldani, készíts screenshot-ot:
     - n8n execution log-ról
     - Hibaüzenetről
     - Google Sheet-ről

### Kapcsolat

Ha segítségre van szükséged:
- **Email:** czunidaniel9@gmail.com
- **Mellékelj:**
  - Screenshot a hibáról
  - Mit próbáltál már
  - Milyen hibaüzenetet kaptál

---

## 📝 Verzió történet

### v2.0 (2025-12-03) - TOVÁBBFEJLESZTETT
- ✅ Email validálás hozzáadva
- ✅ Hiba értesítés (email alert)
- ✅ Rate limiting (50 email/futás)
- ✅ Időbélyeg naplózás
- ✅ Végrehajtás logolás
- ✅ Automatikus hibakezelés
- ✅ Új Sheet séma (egyszerűbb oszlopnevek)

### v1.0 (2024) - EREDETI
- Alapfunkciók: Sheet olvasás, email küldés, Küldve jelölés

---

**🎉 Gratulálunk! Sikeresen beállítottad a továbbfejlesztett Sportpajzs email automatizálást!**

Ha bármilyen kérdésed van, nézd meg a [TROUBLESHOOT-HU.md](./TROUBLESHOOT-HU.md) fájlt vagy írj emailt! 📧
