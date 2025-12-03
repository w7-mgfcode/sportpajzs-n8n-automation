# ❓ Gyakran Ismételt Kérdések (GYIK)

**Verzió:** 2.0.0
**Utolsó frissítés:** 2025-12-03

---

## 📋 Tartalomjegyzék

- [Általános](#általános)
- [Telepítés](#telepítés)
- [Használat](#használat)
- [Hibák & Problémák](#hibák--problémák)
- [Limitek & Kvóták](#limitek--kvóták)
- [Haladó](#haladó)

---

## Általános

### Mi ez a projekt?

n8n workflow automatizálás, amely automatikusan küld bemutatkozó emaileket a Sportpajzs termékről potenciális partnereknek (sportklubok, egyesületek).

### Kinek készült?

Biztosítási és befektetési tanácsadóknak, akik szeretnének automatizálni cold outreach kampányokat.

### Mennyibe kerül?

**Ingyenes!**
- n8n: Cloud (ingyenes tier) vagy Self-hosted (ingyenes)
- Gmail API: Ingyenes (500 email/nap)
- Google Sheets API: Ingyenes

**Opcionális költségek:**
- n8n Cloud Pro: €20/hó (ha több workflow kell)
- Google Workspace: €6/hó (ha 2000 email/nap kell)

### Milyen nyelven van a dokumentáció?

**100% magyar!** Minden dokumentáció, hibaüzenet és útmutató magyarul készült.

---

## Telepítés

### Milyen előfeltételei vannak?

✅ **Szükséges:**
- n8n fiók (Cloud vagy Self-hosted)
- Google fiók
- Gmail (Sportpajzs vagy saját)
- Google Sheets hozzáférés

❌ **Nem szükséges:**
- Programozási tudás
- Server/hosting (ha n8n Cloud-ot használsz)
- Fizetős szolgáltatás

### Mennyi időbe telik a telepítés?

**30 perc** a gyors telepítéssel.
- 5 perc: Google Sheet setup
- 10 perc: n8n workflow import
- 10 perc: Beállítás
- 5 perc: Tesztelés

### Kell-e programozni tudni?

**Nem!** Minden lépés vizuálisan (n8n UI-ban) történik. A JavaScript kódok készen vannak a workflow-ban.

### Van-e videós útmutató?

Jelenleg nincs, de készülőben van. Egyelőre a [TELEPITES.md](TELEPITES.md) részletes lépésről-lépésre képekkel illusztrált.

---

## Használat

### Mikor fut a workflow automatikusan?

**Kedd, Szerda, Csütörtök** reggel **10:00 AM** (Budapest időzóna).

**Miért ezek a napok?**
Tapasztalatok szerint hétközi reggel a legjobb időpont cold outreach emailekhez. Hétfő: túl elfoglaltak, Péntek: hétvége előtt kevésbé figyelnek.

### Lehet változtatni az ütemezést?

**Igen!**
1. n8n workflow → **"Schedule Trigger"** node
2. **Cron Expression** mezőben módosíthatod
3. Példák:
   - Minden nap 10:00: `0 0 10 * * *`
   - Hétfő-Péntek 9:00: `0 0 9 * * 1-5`
   - Csak Kedd 14:00: `0 0 14 * * 2`

### Hány emailt küld egyszerre?

**Maximum 50 email per futás.**

**Miért?**
- Védi a Gmail napi limitjét (500 email/nap)
- Csökkenti a spam jelzés kockázatát
- Jobb deliverability

**Ha több mint 50 unsent kontaktom van?**
A következő futáskor küldi a maradékot. Pl.:
- Kedden: 50 email
- Szerdán: 50 email
- Csütörtökön: 20 email
- **Összesen:** 120 email / 3 nap

### Mit jelent a "Küldve" oszlop?

**Üres "Küldve"** = Még **nem küldött** emailt ennek a kontaktnak.
**Kitöltött "Küldve"** = Már **küldött** (dátum+idő).

A workflow csak azokat a sorokat dolgozza fel, ahol a **Küldve** oszlop **üres**.

### Hogyan tudom újra küldeni egy kontaktnak?

1. Google Sheet-ben keresd meg a kontaktot
2. Töröld ki a **"Küldve"** oszlop értékét (üres cella legyen)
3. Mentsd el
4. A következő futáskor újra küld neki

⚠️ **Vigyázz:** Ne spam-elj! Csak akkor küldj újra, ha indokolt (pl. új ajánlat, válaszolt de később újra megkeresed).

### Hogyan tudok új kontaktot hozzáadni?

1. Nyisd meg a Google Sheet-et
2. Új sor az utolsó sor alá
3. Töltsd ki:
   - **ID:** Egyedi azonosító (pl. SP010)
   - **Név:** Kontakt/szervezet neve
   - **Email:** Email cím
   - **Típus:** Sportklub, Egyesület, stb. (opcionális)
   - **Küldve, Válasz, Megjegyzés:** Hagyd üresen!
4. Mentsd el

Kész! A következő futáskor kiküldi neki az emailt.

### Lehet-e személyre szabni az email szöveget?

**Igen!**

n8n workflow → **"Send Intro Email"** node → **Message** mező

**Placeholder-ek használhatók:**
- `{{ $json.Név }}` → Behelyettesíti a nevet
- `{{ $json.Email }}` → Behelyettesíti az email címet
- `{{ $json.Típus }}` → Behelyettesíti a típust

**Példa:**
```html
<p>Tisztelt {{ $json.Név }}!</p>
<p>Azért keresem meg, mert a {{ $json.Típus }} kategóriában...</p>
```

### Honnan tudom hogy működik?

**3 hely ellenőrizd:**

1. **Google Sheet:**
   - "Küldve" oszlop kitöltődött időbélyeggel?

2. **n8n Executions:**
   - n8n → Executions
   - Legutóbbi futás: ✅ zöld?
   - Hány emailt küldött?

3. **Gmail Sent:**
   - Sportpajzs Gmail → Sent folder
   - Látod az elküldött emaileket?

---

## Hibák & Problémák

### A workflow lefut zöldre, de nem küld emailt

**Lehetséges okok & Megoldások:**

1. **Minden kontakthoz már küldött**
   → Adj hozzá új kontaktot ÜRE Küldve oszloppal

2. **Filter node kiszűrte**
   → n8n Executions → nézd meg a "Filter Unsent" node outputját

3. **Gmail credential lejárt**
   → n8n → Credentials → Reauthorize

**Részletes megoldás:** [HIBAELHARITAS.md - Hiba #3](HIBAELHARITAS.md#hiba-3-no-items-to-process---a-workflow-lefut-de-nem-küld-emailt)

### "Invalid credentials" vagy "Expired token"

**Mi történt:** OAuth token lejárt (7 nap után szokott).

**Megoldás:**
1. n8n → Credentials
2. Keresd: "Sportpajzs Gmail" vagy "Google Sheets account"
3. Klikk: **Reauthorize**
4. Google bejelentkezés
5. Engedélyek jóváhagyása

**Megelőzés:** Hetente egyszer ellenőrizd a credentials státuszát.

**Részletes megoldás:** [HIBAELHARITAS.md - Hiba #1](HIBAELHARITAS.md#hiba-1-invalid-credentials-vagy-expired-token)

### "Column not found: Küldve"

**Mi történt:** Sheet oszlopnév nem egyezik a workflow-val.

**Megoldás:**
1. Ellenőrizd az oszlopneveket **pontosan:**
   ```
   ID | Név | Email | Típus | Küldve | Válasz | Megjegyzés
   ```
2. Nincs felesleges szóköz az oszlopnevek végén!
3. Ékezetek fontosak: **Küldve** (nem "küldve" vagy "Kuldve")

**Részletes megoldás:** [HIBAELHARITAS.md - Hiba #2](HIBAELHARITAS.md#hiba-2-column-not-found-küldve-vagy-column-not-found-email)

### Nem kaptam hiba emailt pedig a workflow elbukott

**Lehetséges okok:**

1. **Error Notification workflow nincs aktiválva**
   → n8n → "Error Notification" workflow → Active = ON

2. **Email cím nincs beállítva**
   → "Send Error Alert to Dani" node → Send To: saját email

3. **Credential hiba az error workflow-ban is**
   → Credentials → Personal Gmail → Reauthorize

### A workflow nem fut automatikusan

**Ellenőrzési lista:**

✅ Workflow **Active** = ON?
✅ Schedule Trigger **Expression** helyes? (`0 0 10 * * 2-4`)
✅ **Timezone:** Europe/Budapest?
✅ n8n instance fut? (Cloud: mindig, Self-hosted: ellenőrizd)

**Részletes megoldás:** [HIBAELHARITAS.md - Hiba #7](HIBAELHARITAS.md#hiba-7-workflow-nem-fut-automatikusan-csak-manuálisan)

---

## Limitek & Kvóták

### Hány emailt küldhetek naponta?

**Gmail Personal:** 500 email/nap
**Google Workspace:** 2000 email/nap

**A workflow védelmei:**
- Max 50 email/futás
- 3 futás/hét = max 150 email/hét
- Ez **jóval a limit alatt** van

### Mi történik ha elérem a limitet?

Gmail hibát dob: **"Daily sending quota exceeded"**

**Megoldások:**
1. **Várj 24 órát** (limit resetelődik éjfélkor UTC)
2. **Google Workspace fiók** (2000 email/nap)
3. **Több Gmail fiók** rotációban
4. **Email szolgáltatás** (SendGrid, Mailgun)

**Részletes megoldás:** [HIBAELHARITAS.md - Hiba #5](HIBAELHARITAS.md#hiba-5-rate-limit-exceeded-vagy-daily-sending-quota-exceeded)

### Van-e Sheet méret limit?

**Google Sheets limit:** 10 millió cella / sheet

**Gyakorlatban:** 7 oszlop × 1,428,571 sor = **1.4 millió kontakt**

Ez több mint elég! 😊

### Hány workflow-m lehet n8n-ben?

**n8n Cloud Free:** 5 aktív workflow
**n8n Cloud Starter:** 20 aktív workflow
**n8n Self-hosted:** Korlátlan

Ez a projekt **2 workflow**-t használ (cold-outreach + error-notification).

---

## Haladó

### Lehet-e több típusú emailt küldeni?

**Igen!** Két megközelítés:

**Opció A: Több workflow (egyszerűbb)**
- Duplikáld a cold-outreach workflow-t
- Nevezd át: "Sportpajzs 2", "Életbiztosítás", stb.
- Külön Sheet minden termékhez
- Külön ütemezés

**Opció B: Egy workflow IF node-dal (komplex)**
- IF node a "Típus" oszlop alapján
- Különböző email template branch-ek
- Egy Sheet, több termék

### Lehet-e follow-up emaileket küldeni?

**Jelenleg nem automatikus**, de implementálható:

1. Új oszlop a Sheet-ben: **"Follow-up"** (dátum)
2. Új workflow: **"Follow-up Email"**
3. Trigger: Check if `(Válasz == "Nem válaszolt") AND (Küldve + 7 nap <= Ma)`
4. Küldd a follow-up emailt

Ha érdekel, nyiss egy [GitHub Issue](https://github.com/yourusername/sportpajzs-n8n-automation/issues/new)-t!

### Lehet-e válaszokat automatikusan trackeln?

**Részlegesen:**

Gmail API-val lehet olvasni a válaszokat, de komplex:
- Gmail Thread ID követés
- Válasz detektálás
- Sheet frissítés

**Egyszerűbb megoldás:** Kézi tracking a "Válasz" oszlopban.

### Lehet-e A/B tesztelni az email szöveget?

**Igen!** Két módszer:

**Opció A: Két workflow**
- cold-outreach-A.json (Email verzió A)
- cold-outreach-B.json (Email verzió B)
- 50%-50% split manuálisan a Sheet-ben

**Opció B: Random split node**
- IF node: `{{ Math.random() < 0.5 }}` → Branch A vagy B
- Külön email template branch-ek

### Használhatom más termékekhez is?

**Abszolút!** Csak az email tartalmat kell módosítani:

1. Workflow → "Send Intro Email" node
2. **Subject:** Változtasd meg
3. **Message:** Cseréld ki a Sportpajzs szöveget
4. **Pecsét kép:** Cseréld ki (ha van)

Ez a workflow működik **bármilyen** cold outreach kampányhoz!

### Hol tárolódnak a credentials?

**n8n Cloud:** n8n szervereken (titkosítva)
**n8n Self-hosted:** Lokális adatbázisban (titkosítva)

**Fontos:** A credentials **SOHA nem kerülnek** a GitHub repo-ba (.gitignore védi).

### Lehet-e export statisztikát?

**n8n Executions log-ból:** Igen, de manuálisan

**Google Sheets-ből:** Könnyebb!
- "Küldve" oszlop alapján megszámolhatod
- Formula: `=COUNTA(E:E) - 1` (hány email lett küldve)
- Pivot table: Hány email / hónap

**Dashboard:** Jelenleg nincs, de fejleszthető (FastAPI + Charts).

---

## 🆘 Nem találod a választ?

1. **Nézd meg a dokumentációt:**
   - [Telepítési útmutató](TELEPITES.md)
   - [Hibaelhárítás](HIBAELHARITAS.md)

2. **GitHub Issues:**
   - [Új kérdés](https://github.com/yourusername/sportpajzs-n8n-automation/issues/new)
   - Keres létező válaszokat

3. **Email:**
   - czunidaniel9@gmail.com

---

**Utolsó frissítés:** 2025-12-03 | **Verzió:** 2.0.0
