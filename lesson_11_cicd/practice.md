# 🧪 Amaliy Mashqlar — Dars 11: CI/CD va GitHub Actions

Ushbu amaliy mashqlar orqali GitHub Actions yordamida Django loyihasini test qilish va avtomatlashtirish bo‘yicha ko‘nikmalar hosil qilasiz.

---

## 1️⃣ Boshlang‘ich tayyorgarlik

### 1.1. Django loyihangizni GitHub'ga joylashtiring

```bash
git init
git add .
git commit -m "Initial commit"
gh repo create my-django-app --public --source=. --push
```

> ⚠️ `gh` bu GitHub CLI — o‘rnatilmagan bo‘lsa, o‘rnating: `https://cli.github.com/`

---

## 2️⃣ GitHub Actions uchun fayl yarating

### 2.1. Quyidagi yo‘nalishda yangi fayl yarating:

```
.github/workflows/django.yml
```

### 2.2. Quyidagi YAML kodni joylashtiring:

```yaml
name: Django CI

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: mydb
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - name: Kodni yuklash
        uses: actions/checkout@v4

      - name: Python o‘rnatish
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Kutubxonalarni o‘rnatish
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Testlarni ishga tushirish
        run: |
          python manage.py test
```

---

## 3️⃣ GitHub Secrets bilan ishlash

### 3.1. Quyidagi maxfiy kalitlarni sozlang:

| Secret nomi      | Tavsif                                 |
| ---------------- | -------------------------------------- |
| `SECRET_KEY`     | Django loyihangizning maxfiy kaliti    |
| `DATABASE_URL`   | PostgreSQL ulanish satri               |
| `RENDER_API_KEY` | (Keyingi dars uchun) Render API kaliti |

---

## 4️⃣ Testlar yozilganligiga ishonch hosil qiling

```python
# myapp/tests.py
from django.test import TestCase

class BasicTest(TestCase):
    def test_addition(self):
        self.assertEqual(2 + 2, 4)
```

---

## 5️⃣ GitHub Actions'ni ishga tushiring

```bash
git add .github/workflows/django.yml
git commit -m "Add GitHub Actions CI config"
git push origin main
```

✅ **Natijani ko‘rish:**

- GitHub repo sahifasiga o‘ting
- `Actions` bo‘limiga kirib, workflow'ning holatini kuzating

---

## 🔁 Qo‘shimcha mashqlar

- `develop` branch uchun alohida workflow yozing.
- `pull_request` holati uchun testlarni ishga tushiring.
- `matrix strategy` bilan Python 3.9, 3.10, 3.11 versiyalarini test qiling.
- Kod testlarini `pytest` orqali bajaring.

---

## 🏁 Yakuniy vazifa

CI/CD to‘liq ishlashini ta'minlang:

- [x] Kod push qilganda testlar avtomatik ishlashi
- [x] Xatolik bo‘lsa, GitHub Actions'da aniqlanishi
- [x] Secrets yordamida xavfsiz ishlash

---

**Tabriklaymiz!** Siz CI/CD asoslarini amaliyotda qo‘lladingiz!
