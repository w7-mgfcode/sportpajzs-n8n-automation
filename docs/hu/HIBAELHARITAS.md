# Hibaelhárítási Útmutató - Sportpajzs Email Automatizálás

**Verzió:** 2.0
**Dátum:** 2025-12-03
**Nyelv:** Magyar

---

## 📋 Tartalomjegyzék

1. [Gyakori hibák és megoldások](#-gyakori-hibák-és-megoldások)
2. [Hibák megelőzése](#-hibák-megelőzése)
3. [n8n diagnosztika](#-n8n-diagnosztika)
4. [Támogatás kérése](#-támogatás-kérése)

---

## 🔴 Gyakori hibák és megoldások

### HIBA #1: "Invalid credentials" vagy "Expired token"

#### ❓ Mi történt?

A Gmail vagy Google Sheets OAuth token lejárt. A Google biztonsági okokból időnként (általában 7 nap után) újra-engedélyezést kér.

#### ✅ Megoldás (lépésről lépésre):

1. **Nyisd meg az n8n-t**
2. Bal oldali menüben: **Credentials**
3. **Keresd meg** a hibás credential-t:
   - "Sportpajzs Gmail" VAGY
   - "Google Sheets account"
4. **Klikkelj rá** a credential nevére
5. Jobbra fent látod a státuszt:
   - ✅ Connected = rendben
   - ❌ Disconnected = újra kell engedélyezni
6. **Klikk:** "Reauthorize" vagy "Reconnect" gomb
7. **Google bejelentkezés** ablak nyílik meg
8. Válaszd ki a fiókod (Sportpajzs Gmail vagy a saját)
9. **Hagyd jóvá** az engedélyeket:
   - "Gmail olvasás és küldés"
   - "Google Sheets olvasás és írás"
10. **Visszairányít** az n8n-re
11. Ellenőrizd: ✅ Connected státusz

#### 🎯 Tipp:

**Előre beállított emlékeztető:**
- Heti 1x nézd meg: Credentials → Minden credential Connected?
- Ha lejár: azonnal újra-engedélyezés

---

### HIBA #2: "Column not found: Küldve" vagy "Column not found: Email"

#### ❓ Mi történt?

A Google Sheet oszlopneve nem egyezik azzal, amit a workflow vár.

**Lehetséges okok:**
- Elírtad az oszlopnevet
- Felesleges szóköz van az oszlopnév végén
- Hiányzik egy oszlop
- Átnevezted az oszlopot

#### ✅ Megoldás (lépésről lépésre):

1. **Nyisd meg a Google Sheet-et**
2. **Ellenőrizd az első sort** (header sor):

   **HELYES oszlopnevek (PONTOSAN így):**
   ```
   ID | Név | Email | Típus | Küldve | Válasz | Megjegyzés
   ```

3. **Gyakori hibák:**

   | Hibás | Helyes |
   |-------|--------|
   | küldve (kis k) | Küldve (nagy K) |
   | email (kicsi) | Email (nagy E) |
   | "Email " (szóköz végén) | "Email" (nincs szóköz) |
   | "Küldve?" | "Küldve" |

4. **Javítsd ki** az oszlopneveket pontosan a helyes formára
5. **Mentsd el** a Sheet-et (Ctrl+S vagy automatikus mentés)
6. **n8n-ben futtasd újra** a workflow-t

#### 🎯 Tipp:

**Másolás-beillesztés módszer:**
- Nyisd meg: `sportbiztositas-template.csv`
- Másold ki az első sort
- Illeszd be a Google Sheet első sorába
- Ez garantálja a pontos formátumot!

---

### HIBA #3: "No items to process" - A workflow lefut, de nem küld emailt

#### ❓ Mi történt?

A workflow sikeresen lefutott, de 0 emailt küldött.

**Lehetséges okok:**
1. Minden kontakthoz már küldött emailt (Küldve oszlop kitöltve)
2. Nincs egyetlen valid kontakt sem a Sheet-ben
3. Minden email cím invalid formátumú
4. A filter node kiszűrte az összes kontaktot

#### ✅ Megoldás (lépésről lépésre):

**4.1. Ellenőrizd a Google Sheet-et:**

1. Nyisd meg a Sheet-et
2. **Nézd meg a "Küldve" oszlopot:**
   - Van-e **legalább 1 üres** cella?
   - Ha minden kitöltve → nincs kit megkeresni

**4.2. Adj hozzá új kontaktot VAGY törölj "Küldve" értéket:**

**Új kontakt hozzáadása:**
```
SP010 | Új Sportklub | ujkontakt@example.com | Sportklub | [ÜRESEN HAGYD] | |
```

**VAGY régi kontakt újra-küldés:**
- Válassz egy sort
- Töröld ki a "Küldve" oszlop értékét (üres cella lesz)
- Ez újra küldhető lesz

**4.3. Ellenőrizd az email formátumot:**

Minden email címnek **valid** formátumúnak kell lennie:

| Valid ✅ | Invalid ❌ |
|----------|-----------|
| info@sportklub.hu | info sportklub hu |
| kontakt@example.com | kontakt@example |
| hello@domain.co.uk | hello@ |
| | @domain.com |

**4.4. n8n diagnosztika:**

1. n8n → Executions
2. Klikkelj a legutóbbi futásra
3. **Nézd meg minden node outputját:**
   - "Read Contacts from Sheet" → Hány item olvasott be?
   - "Validate Emails" → Hány maradt utána?
   - "Filter Unsent Contacts" → Hány maradt utána?
4. **Azonosítsd** melyik node-nál lett 0 item

---

### HIBA #4: Workflow lefut, de az email nem érkezik meg

#### ❓ Mi történt?

Az n8n szerint sikeres (✅ zöld), de az email mégsem érkezett meg.

**Lehetséges okok:**
1. Email a spam mappában van
2. Email cím elírva a Sheet-ben
3. Címzett szerver blokkolta
4. Gmail küldési hiba (nem látszik az n8n-ben)

#### ✅ Megoldás (lépésről lépésre):

**5.1. Ellenőrizd a Gmail Sent (Elküldött) mappát:**

1. Bejelentkezés: **Sportpajzs Gmail fiók**
2. Bal oldalon: **Sent** (Elküldött)
3. **Keresd meg** az emailt:
   - Dátum: ma vagy tegnap?
   - Címzett: a kontakt email címe?
4. **Ha NINCS ott → nem is küldte el** (n8n hiba)
5. **Ha OTT VAN → elküldött** (ellenőrizd a címzett spam-jét)

**5.2. Ellenőrizd a spam mappát (címzettnél):**

- Az első emailek gyakran spam-be kerülnek
- Kérd meg a címzettet: nézze meg a spam mappát
- Ha ott van: "Not spam" / "Nem spam" jelölés

**5.3. Ellenőrizd az email címet a Sheet-ben:**

Gyakori elírások:
| Hibás | Helyes |
|-------|--------|
| info@gmai.com | info@gmail.com |
| kontakt @domain.hu (szóköz) | kontakt@domain.hu |
| DUPLA pont: info..@domain | info@domain |

**5.4. Küldj teszt emailt magadnak:**

1. Sheet-ben adj hozzá sort a **saját emaileddel**
2. Küldve oszlop = üres
3. Futtasd a workflow-t
4. Ha MEGKAPOD → minden rendben, címzett oldali probléma
5. Ha NEM KAPOD MEG → n8n vagy Gmail probléma

---

### HIBA #5: "Rate limit exceeded" vagy "Daily sending quota exceeded"

#### ❓ Mi történt?

Gmail napi email limit elérve.

**Limitek:**
- **Personal Gmail:** 500 email/nap
- **Google Workspace:** 2000 email/nap

#### ✅ Megoldás (lépésről lépésre):

**6.1. Ellenőrizd hány emailt küldtél ma:**

1. Sportpajzs Gmail → **Sent** mappa
2. Szűrés: **Today** (Ma)
3. **Számold meg** (vagy görgess végig)
4. Ha **500 felett** → elérted a limit-et

**6.2. Várj 24 órát:**

- A limit **minden nap éjfélkor** (UTC időzóna szerint) resetelődik
- **Magyar idő szerint:** körülbelül hajnalban

**6.3. Hosszú távú megoldások:**

**Opció A: Google Workspace (fizetős)**
- Limit: 2000 email/nap
- Költség: ~€6/hó/felhasználó
- Professzionális email cím

**Opció B: Több Gmail fiók (ingyenes)**
- 2-3 Gmail fiók rotációban
- Mindegyik 500 email/nap
- Összesen 1000-1500 email/nap

**Opció C: Email szolgáltatás (SendGrid, Mailgun)**
- Nagyobb limit
- Jobb deliverability
- Költség: változó

**Melyiket válaszd?**
- **0-400 email/nap:** Maradj Personal Gmail-nél ✅
- **400-1000 email/nap:** Google Workspace vagy több Gmail
- **1000+ email/nap:** Email szolgáltatás (SendGrid)

---

### HIBA #6: "Cannot read properties of undefined" vagy JavaScript hiba

#### ❓ Mi történt?

A Code node (JavaScript) hibát dob, mert hiányzó adatot próbál olvasni.

**Lehetséges okok:**
- Üres cella a Sheet-ben (hiányzik Név vagy Email)
- Oszlopnév nem egyezik a kódban

#### ✅ Megoldás (lépésről lépésre):

**7.1. Ellenőrizd a hiányzó adatokat:**

Minden sorban **kötelező** kitölteni:
- **ID** - Egyedi azonosító
- **Név** - Kapcsolat neve
- **Email** - Email cím

**Opcionális** (lehet üres):
- Típus
- Küldve (üres ha még nem küldött)
- Válasz
- Megjegyzés

**7.2. Példa helytelen sor:**
```
SP005 | | ujkontakt@example.com | | | |
         ^
         Név hiányzik!
```

**7.3. Példa helyes sor:**
```
SP005 | Sportklub Név | ujkontakt@example.com | Sportklub | | |
```

---

### HIBA #7: Workflow nem fut automatikusan (csak manuálisan)

#### ❓ Mi történt?

Manuálisan (Execute Workflow gomb) működik, de az automatikus ütemezett futás nem történik meg.

**Lehetséges okok:**
1. Workflow **nincs aktiválva**
2. Schedule node **nincs beállítva**
3. n8n instance **nem fut** (leállt vagy újraindult)

#### ✅ Megoldás (lépésről lépésre):

**8.1. Ellenőrizd a workflow aktiválás:**

1. Nyisd meg a workflow-t n8n-ben
2. Jobb felül nézd meg: **Active** kapcsoló
3. Ha **OFF** (szürke) → kapcsold **ON** (zöld)
4. Mentsd el a workflow-t

**8.2. Ellenőrizd a Schedule Trigger node-ot:**

1. Klikkelj: "Schedule Trigger" node
2. Ellenőrizd:
   - **Trigger Times:** Cron Expression
   - **Expression:** `0 0 10 * * 2-4`
   - **Timezone:** Europe/Budapest

**8.3. Teszteld az ütemezést:**

Kis trükk a teszteléshez:
1. Változtasd a cron expression-t **következő 5 percre:**
   - Pl. ha most 14:30, állítsd: `35 14 * * *` (14:35)
2. Aktiváld a workflow-t
3. Várj az időpontig
4. Ellenőrizd: Executions → Új futás jelent meg?
5. Ha működik: **állítsd vissza** az eredeti ütemezésre

**8.4. Ellenőrizd az n8n instance állapotát:**

- n8n Cloud: ritkán leáll
- Self-hosted: szerver restart után újra kell indítani

---

## 🛡️ Hibák megelőzése

### Heti karbantartás (5 perc)

**Minden héten egyszer (pl. hétfő reggel):**

1. ✅ **Credentials ellenőrzés:**
   - n8n → Credentials
   - Gmail: ✅ Connected?
   - Sheets: ✅ Connected?

2. ✅ **Executions áttekintés:**
   - n8n → Executions
   - Legutóbbi 3 futás: mind ✅ zöld?
   - Ha piros: nézd meg mi volt a hiba

3. ✅ **Gmail quota:**
   - Gmail Sent → Hány email múlt héten?
   - Közel a 500/nap limithez?

### Havi karbantartás (15 perc)

**Minden hónap elején:**

1. ✅ **Sheet tisztítás:**
   - Régi kontaktok archiválása
   - Válaszolók jelölése ("Válasz" oszlop)
   - Megjegyzések frissítése

2. ✅ **Email template frissítés:**
   - Aktuális-e még a szöveg?
   - Linkek működnek?
   - Kép URL érvényes?

3. ✅ **Statisztika:**
   - Hány email küldött az elmúlt hónapban?
   - Hány válasz érkezett?
   - Milyen a válasz arány (response rate)?

### Megelőző tippek

**🎯 TI

PP #1: Mindig tesztelj magadnak először**
- Új kontakt hozzáadása előtt: adj hozzá 1 sort a saját emaileddel
- Futtasd manuálisan
- Ellenőrizd hogy megérkezik és jól néz ki

**🎯 TIPP #2: Ne módosítsd az oszlopneveket**
- Ha muszáj változtatni: frissítsd a workflow-t is!
- Jobb: Hagyd az eredeti oszlopneveket, csak a tartalom változzon

**🎯 TIPP #3: Hetente egyszer újra-engedélyezés**
- Még ha Connected is a credential, újra-engedélyezheted
- Ez garantálja hogy ne járjon le váratlanul

**🎯 TIPP #4: Backup a Sheet-ről**
- Havonta: File → Download → CSV
- Mentsd el a gépedre (backup)
- Ha véletlenül törlöd: vissza tudod állítani

---

## 🔍 n8n diagnosztika

### Hogyan találd meg a hibát az n8n-ben?

#### 1. Executions megnyitása

1. n8n bal oldali menü: **Executions**
2. Látod a futások listáját:
   - ✅ Zöld = Sikeres
   - ❌ Piros = Hiba

#### 2. Hibás futás részletek

1. **Klikkelj** a piros futásra
2. Megnyílik a workflow végrehajtási nézet
3. **Piros node** = hol történt a hiba

#### 3. Hiba részletek olvasása

1. **Klikkelj a piros node-ra**
2. Jobb oldalon megjelenik:
   - **Error Message:** Mi volt a hiba?
   - **Error Type:** Milyen típusú hiba?
   - **Stack Trace:** Részletes technikai infó

#### 4. Output ellenőrzés (minden node)

Klikkelj végig **minden node-on** (zöld is!):
- **Input:** Mi ment be a node-ba? (hány item)
- **Output:** Mi jött ki? (hány item)
- **Kimenet:** Ha 0 item → kiszűrődött minden

**Példa diagnosztika:**

```
Schedule Trigger → Output: 1 item ✅
↓
Read Contacts from Sheet → Output: 15 items ✅
↓
Validate Emails → Output: 12 items ✅ (3 invalid kiszűrve)
↓
Filter Unsent Contacts → Output: 5 items ✅ (7-nek már küldött)
↓
Rate Limit → Output: 5 items ✅ (50 alatt, mind mehet)
↓
Send Intro Email → ERROR ❌ (Gmail credential lejárt)
```

**Konklúzió:** Gmail credential újra-engedélyezés szükséges!

---

## 📞 Támogatás kérése

### Mikor kérj segítséget?

Ha a fenti megoldások **nem működtek** és:
- Már 30+ percet próbálkozol
- Nem érted a hibaüzenetet
- Félsz hogy elrontasz valamit

→ **Ne habozz segítséget kérni!**

### Mit küldj el emailben?

**📧 Email sablon:**

```
Tárgy: n8n Workflow Hiba - Sportpajzs Cold Outreach

Szia!

Probléma van a Sportpajzs email automatizálással.

🔴 MI A HIBA?
[Írd le mit látsz, mi nem működik]
Pl.: "A workflow lefut zöldre, de nem küld emailt"

📋 MIT PRÓBÁLTAM MÁR?
1. [Első próbálkozás]
2. [Második próbálkozás]
3. ...

💻 n8n HIBA ÜZENET:
[Másold be a teljes hibaüzenetet]

📸 MELLÉKLETEK:
- Screenshot #1: n8n execution log
- Screenshot #2: Google Sheet (első 5 sor)
- Screenshot #3: Hibaüzenet

Köszönöm a segítséget!
Dani
```

### Screenshot készítés

**Windows:**
- **Win + Shift + S** → kiválasztod a területet → vágólapra másolódik

**Mac:**
- **Cmd + Shift + 4** → kiválasztod a területet → asztalra menti

**Mit fotózz le:**
1. **n8n execution log:** teljes workflow nézet a futás közben
2. **Piros node részletei:** error message
3. **Google Sheet:** első 10 sor (header + adatok)
4. **Credentials oldal:** státuszok (takard ki a sensitive adatokat!)

---

## 🆘 Gyors hivatkozások

| Probléma | Gyors megoldás |
|----------|----------------|
| OAuth lejárt | [Hiba #1](#hiba-1-invalid-credentials-vagy-expired-token) |
| Oszlop nem található | [Hiba #2](#hiba-2-column-not-found-küldve-vagy-column-not-found-email) |
| Nem küld emailt | [Hiba #3](#hiba-3-no-items-to-process---a-workflow-lefut-de-nem-küld-emailt) |
| Email nem érkezik | [Hiba #4](#hiba-4-workflow-lefut-de-az-email-nem-érkezik-meg) |
| Limit elérve | [Hiba #5](#hiba-5-rate-limit-exceeded-vagy-daily-sending-quota-exceeded) |
| JavaScript hiba | [Hiba #6](#hiba-6-cannot-read-properties-of-undefined-vagy-javascript-hiba) |
| Nem fut automatikusan | [Hiba #7](#hiba-7-workflow-nem-fut-automatikusan-csak-manuálisan) |

---

## 📚 További olvasnivaló

- **Használati útmutató:** [README-HU.md](./README-HU.md)
- **n8n Dokumentáció:** https://docs.n8n.io
- **Gmail API Limitek:** https://support.google.com/mail/answer/22839

---

**🔧 Emlékezz: A legtöbb hiba 5 perc alatt megoldható, ha tudod hol keress! Ez az útmutató itt van hogy segítsen.**

Ha bármi kérdésed van, írj bátran! 📧
