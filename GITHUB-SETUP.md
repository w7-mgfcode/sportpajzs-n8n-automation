# GitHub Repository Setup Guide

This guide will help you push the Sportpajzs n8n automation repository to GitHub and create the v2.0.0 release.

---

## Prerequisites

- Git installed and configured
- GitHub account
- Repository already initialized locally (✅ Done - commit 36fb532)

---

## Step 1: Create GitHub Repository

### Option A: Via GitHub Web Interface (Recommended)

1. Go to [GitHub](https://github.com)
2. Click **"+"** (top right) → **"New repository"**
3. Fill in repository details:
   - **Repository name:** `sportpajzs-n8n-automation`
   - **Description:** `n8n workflow automatizálás sportbiztosítási emailekhez - Magyar nyelvű dokumentáció`
   - **Visibility:**
     - ✅ **Public** (recommended - easier for Dani to access)
     - Or **Private** (if you want to control access)
   - **Initialize repository:**
     - ❌ **DO NOT** check "Add a README file"
     - ❌ **DO NOT** check "Add .gitignore"
     - ❌ **DO NOT** check "Choose a license"
     - (We already have these files locally)
4. Click **"Create repository"**

### Option B: Via GitHub CLI

```bash
gh repo create sportpajzs-n8n-automation \
  --public \
  --description "n8n workflow automatizálás sportbiztosítási emailekhez - Magyar nyelvű dokumentáció" \
  --source=. \
  --remote=origin
```

---

## Step 2: Connect Local Repository to GitHub

### Get the remote URL

After creating the repository on GitHub, you'll see a setup page with the remote URL. It looks like:

```
https://github.com/w7-mgfcode/sportpajzs-n8n-automation.git
```

### Add the remote

```bash
cd /home/w7-shellsnake/w7-DEV_X1/external_FB/czuni_dani/sportpajzs-n8n-automation

# Add GitHub remote
git remote add origin https://github.com/w7-mgfcode/sportpajzs-n8n-automation.git

# Verify remote was added
git remote -v
```

**Expected output:**
```
origin  https://github.com/w7-mgfcode/sportpajzs-n8n-automation.git (fetch)
origin  https://github.com/w7-mgfcode/sportpajzs-n8n-automation.git (push)
```

---

## Step 3: Rename Branch to 'main' (Optional but Recommended)

GitHub uses `main` as the default branch name. Our local repo uses `master`.

```bash
# Rename master to main
git branch -M main
```

---

## Step 4: Push to GitHub

### Push all commits and set upstream

```bash
git push -u origin main
```

**Expected output:**
```
Enumerating objects: 14, done.
Counting objects: 100% (14/14), done.
Delta compression using up to 8 threads
Compressing objects: 100% (11/11), done.
Writing objects: 100% (14/14), XX.XX KiB | X.XX MiB/s, done.
Total 14 (delta 0), reused 0 (delta 0)
To https://github.com/w7-mgfcode/sportpajzs-n8n-automation.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'.
```

### If you get authentication errors:

**Option A: Personal Access Token (Recommended)**

1. Go to GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Click "Generate new token (classic)"
3. Scopes to select:
   - ✅ `repo` (all repo permissions)
   - ✅ `workflow` (if using GitHub Actions)
4. Copy the token (you won't see it again!)
5. Use token as password when pushing:
   ```bash
   Username: YOUR_USERNAME
   Password: ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXX
   ```

**Option B: SSH Key**

If you prefer SSH:
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Add to ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Copy public key to clipboard
cat ~/.ssh/id_ed25519.pub

# Add to GitHub: Settings → SSH and GPG keys → New SSH key
```

Then change remote to SSH:
```bash
git remote set-url origin git@github.com:w7-mgfcode/sportpajzs-n8n-automation.git
git push -u origin main
```

---

## Step 5: Configure Repository Settings

### A. Add Topics

On GitHub repository page:
1. Click ⚙️ (settings gear next to "About")
2. Add topics:
   - `n8n`
   - `automation`
   - `email-marketing`
   - `hungarian`
   - `insurance`
   - `workflow`
   - `cold-outreach`
3. Click **"Save changes"**

### B. Update Description

Ensure the description is set to:
```
n8n workflow automatizálás sportbiztosítási emailekhez - Magyar nyelvű dokumentáció
```

### C. Set Website (Optional)

If you have a demo or documentation site, add it here.

---

## Step 6: Create v2.0.0 Release

### Via GitHub Web Interface (Recommended)

1. Go to your repository on GitHub
2. Click **"Releases"** (right sidebar)
3. Click **"Create a new release"**
4. Fill in release details:

   **Tag version:**
   ```
   v2.0.0
   ```

   **Release title:**
   ```
   🚀 v2.0.0 - Enhanced Cold Outreach Automation
   ```

   **Description (Hungarian):**
   ```markdown
   # 🎉 Sportpajzs n8n Automatizálás v2.0.0

   ## ✨ Új Funkciók

   - ✅ **Email validálás** - Ellenőrzi az email formátumot küldés előtt
   - ✅ **Hibaértesítés** - Automatikus email értesítés ha a workflow hibát dob
   - ✅ **Rate limiting** - Maximum 50 email per futás (védi a Gmail limitjét)
   - ✅ **Időbélyeg naplózás** - Teljes dátum + idő (pl. 2025-12-03 10:15:30)
   - ✅ **Végrehajtás logolás** - Console log minden futás után
   - ✅ **Automatikus hibakezelés** - Try/Catch wrapper node-ok

   ## 🔧 Változtatások

   ### Új Sheet Séma
   **Régi (v1.0):**
   ```
   sheetID, Név , e-mail cím , Küldve, row_number
   ```

   **Új (v2.0):**
   ```
   ID, Név, Email, Típus, Küldve, Válasz, Megjegyzés
   ```

   - ❌ Removed: `sheetID` → ✅ `ID`
   - ❌ Removed: trailing space-ek
   - ✅ Added: `Típus`, `Válasz`, `Megjegyzés`

   ## 📚 Dokumentáció

   - 🇭🇺 **129+ oldal magyar nyelvű dokumentáció**
   - 📖 [Telepítési útmutató](docs/hu/TELEPITES.md) - 73 oldal
   - 🔧 [Hibaelhárítás](docs/hu/HIBAELHARITAS.md) - 45 oldal
   - ❓ [Gyakori kérdések](docs/hu/GYIK.md)

   ## 📥 Telepítés

   ### Gyors telepítés

   1. **Töltsd le ezt a release-t** (ZIP fájl lent)
   2. **Google Sheet készítése** - Importáld: `templates/sportbiztositas-template.csv`
   3. **n8n workflow import** - Importáld: `workflows/cold-outreach-enhanced.json`
   4. **Credential beállítás** - Gmail + Google Sheets OAuth
   5. **Teszt futtatás** - Manual execution

   ### Részletes telepítés

   Kövesd a [TELEPITES.md](docs/hu/TELEPITES.md) útmutatót lépésről-lépésre.

   ## ⚠️ Frissítés v1.0-ról

   Ha v1.0-t használod, kövesd a [CHANGELOG.md](CHANGELOG.md) migrálási útmutatóját!

   **Fontos:** Az új workflow NEM kompatibilis a régi Sheet sémával!

   ## 🆘 Támogatás

   - 📖 [Dokumentáció](docs/hu/)
   - 🐛 [GitHub Issues](https://github.com/w7-mgfcode/sportpajzs-n8n-automation/issues)
   - 📧 Email: czunidaniel9@gmail.com

   ---

   **Teljes változtatási lista:** [CHANGELOG.md](CHANGELOG.md)
   ```

5. **Attach files** (optional but recommended):
   - Click "Attach binaries by dropping them here or selecting them."
   - Upload individual files if needed, or GitHub will auto-generate source code ZIP

6. **Set as latest release:** ✅ Check this box

7. Click **"Publish release"**

### Via GitHub CLI

```bash
gh release create v2.0.0 \
  --title "🚀 v2.0.0 - Enhanced Cold Outreach Automation" \
  --notes-file RELEASE_NOTES.md \
  --latest
```

(Create `RELEASE_NOTES.md` with the Hungarian description above)

---

## Step 7: Verify Repository

### Checklist

Go to your GitHub repository page and verify:

- ✅ README.md displays correctly with badges
- ✅ All folders visible: `workflows/`, `templates/`, `docs/`
- ✅ LICENSE file shows "MIT License"
- ✅ Topics are set (n8n, automation, hungarian, etc.)
- ✅ Release v2.0.0 is published
- ✅ "Latest" badge appears on v2.0.0 release
- ✅ Download ZIP button works

---

## Step 8: Share with Dani

### Email Template (Hungarian)

```
Tárgy: ✅ Sportpajzs n8n Automatizálás - Kész a v2.0.0!

Szia Dániel!

Elkészítettem a javított n8n workflow automatizálást! 🎉

🔗 **GitHub Repository:**
https://github.com/w7-mgfcode/sportpajzs-n8n-automation

## 📥 Letöltés

**Gyors letöltés:**
1. Menj a repo-ra: [Link fent]
2. Klikk: **Code** (zöld gomb) → **Download ZIP**
3. Csomagold ki

**VAGY**

**Release letöltés (ajánlott):**
1. Menj: https://github.com/w7-mgfcode/sportpajzs-n8n-automation/releases/tag/v2.0.0
2. Töltsd le: **Source code (zip)**

## 📚 Dokumentáció (Magyarul!)

Minden lépésről-lépésre le van írva:

- **Telepítés:** `docs/hu/TELEPITES.md` (73 oldal)
- **Hibaelhárítás:** `docs/hu/HIBAELHARITAS.md` (45 oldal)
- **GYIK:** `docs/hu/GYIK.md`

## ✨ Új Funkciók v2.0-ban

- ✅ Email validálás
- ✅ Hibaértesítés (kapsz emailt ha valami elromlik)
- ✅ Rate limiting (max 50 email/futás)
- ✅ Jobb Sheet séma
- ✅ Időbélyeg naplózás

## 🚀 Gyors Start (5 lépés)

1. **Google Sheet** - Importáld: `templates/sportbiztositas-template.csv`
2. **n8n workflow** - Importáld: `workflows/cold-outreach-enhanced.json`
3. **Error notification** - Importáld: `workflows/error-notification.json`
4. **Credentials** - Gmail + Sheets OAuth (ellenőrizd)
5. **Teszt** - Manual execution 1 kontakttal

## 🆘 Ha elakadsz

- Nézd meg a dokumentációt (minden benne van!)
- GitHub Issues: Ha találsz hibát vagy kérdésed van
- Email: Válaszolj erre az emailre

Sok sikert! 🚀

---
Ha bármi kérdésed van, nyugodtan írj!

Üdv,
[Your Name]
```

---

## Step 9: Optional - Enable GitHub Actions

If you want to add CI/CD in the future:

1. Create `.github/workflows/` directory
2. Add workflow YAML files for:
   - Automated testing
   - Linting JSON files
   - Release automation

**Example: JSON Validation Workflow**

Create `.github/workflows/validate-json.yml`:

```yaml
name: Validate n8n Workflows

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Validate JSON files
        run: |
          for file in workflows/*.json; do
            echo "Validating $file"
            jq empty "$file" || exit 1
          done
```

---

## Troubleshooting

### "Permission denied (publickey)"

Use HTTPS instead of SSH, or configure SSH key properly.

### "Repository not found"

Check:
- Repository name is correct
- You have access to the repository
- Remote URL is correct (`git remote -v`)

### "Updates were rejected because the remote contains work"

You created the repo with README/LICENSE on GitHub. Either:
1. Pull first: `git pull origin main --allow-unrelated-histories`
2. Or delete and recreate repo without initialization files

### "Large files detected"

If any file is >100MB, you'll need Git LFS:
```bash
git lfs install
git lfs track "*.large-extension"
git add .gitattributes
git commit -m "Add Git LFS"
```

---

## Next Steps

After pushing to GitHub and creating the release:

1. **Share the repository URL with Dani** (use email template above)
2. **Monitor GitHub Issues** for questions from Dani
3. **Update documentation** based on feedback
4. **Create new releases** when you add features or fix bugs

---

## Maintenance

### Creating future releases

```bash
# Make changes, commit them
git add .
git commit -m "feat: Add follow-up email workflow"
git push

# Create new release on GitHub
# Version bump: 2.0.0 → 2.1.0 (new feature) or 2.0.1 (bug fix)
```

Follow semantic versioning:
- **Major (3.0.0):** Breaking changes
- **Minor (2.1.0):** New features, backward compatible
- **Patch (2.0.1):** Bug fixes

---

## Summary

**Commands to run:**

```bash
# 1. Navigate to repository
cd /home/w7-shellsnake/w7-DEV_X1/external_FB/czuni_dani/sportpajzs-n8n-automation

# 2. Add remote
git remote add origin https://github.com/w7-mgfcode/sportpajzs-n8n-automation.git

# 3. Rename branch
git branch -M main

# 4. Push to GitHub
git push -u origin main

# 5. Create release on GitHub (via web interface)
# Go to: https://github.com/w7-mgfcode/sportpajzs-n8n-automation/releases/new

# 6. Share with Dani
# Send email with repository URL and quick start instructions
```

---

**Repository URL:** `https://github.com/w7-mgfcode/sportpajzs-n8n-automation`

**Release v2.0.0 URL:** `https://github.com/w7-mgfcode/sportpajzs-n8n-automation/releases/tag/v2.0.0`

---

✅ **You're all set!** The repository is ready to be shared with Dani.
