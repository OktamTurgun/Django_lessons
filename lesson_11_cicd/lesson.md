# 🚀 Dars 11: CI/CD va GitHub Actions orqali Django loyihasini avtomatlashtirish

---

## 📌 Maqsad:

Ushbu dars orqali siz quyidagilarni o‘rganasiz:

- CI/CD tushunchasi va foydasi
- GitHub Actions orqali Django loyihasini test qilish va deploy qilish
- `.github/workflows/django.yml` fayli tarkibi va ishlash tartibi
- GitHub Secrets bilan xavfsiz kalitlar saqlash

---

## 🧠 1. CI/CD nima?

**CI/CD** — Continuous Integration va Continuous Deployment/Delivery.

| Atama                                   | Tushunchasi                                                                  |
| --------------------------------------- | ---------------------------------------------------------------------------- |
| **CI** (Continuous Integration)         | Har bir kod o‘zgarishidan so‘ng avtomatik testlar ishga tushadi.             |
| **CD** (Continuous Deployment/Delivery) | Testlar muvaffaqiyatli tugasa, loyiha avtomatik ravishda serverga joylanadi. |

**Foydasi:**

- Xatoliklarni erta aniqlaydi
- Ish jarayonini tezlashtiradi
- Jamoaviy ishlashda barqarorlikni ta’minlaydi

---

## 🛠️ 2. GitHub Actions nima?

GitHub Actions — bu **CI/CD vositasi**, u sizga `.yml` fayl orqali quyidagilarni bajarish imkonini beradi:

- Kod o‘zgarishini kuzatish (push/pull)
- Testlarni avtomatik ishga tushirish
- Serverga joylash (deploy)
- Maxfiy kalitlar bilan ishlash (GitHub Secrets)

---

## 📁 3. Strukturasi

```bash
.github/
└── workflows/
    └── django.yml
```

Bu fayl ichida workflow yoziladi, ya’ni qachon va qanday holatda nima ish bajarilishi ko‘rsatiladi.

---

## ⚙️ 4. YAML fayli qismlari

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

## 🔒 5. GitHub Secrets nima?

Maxfiy ma’lumotlar (parollar, API kalitlar) .yml faylda yozilmaydi. Buning o‘rniga Settings > Secrets bo‘limiga quyidagilar qo‘shiladi:

| Secret nomi    | Tavsif                               |
| -------------- | ------------------------------------ |
| RENDER_API_KEY | Render.com API kaliti (deploy uchun) |
| DATABASE_URL   | PostgreSQL uchun ulanish manzili     |
| SECRET_KEY     | Django loyihasining maxfiy kaliti    |

YAML faylda shunday ishlatiladi:

```yaml
env:
  SECRET_KEY: ${{ secrets.SECRET_KEY }}
```

---

## 🚀 6. CI/CD qanday ishlaydi?

1. Siz `main` branchga kod push qilasiz.
2. `.github/workflows/django.yml` ishga tushadi.
3. GitHub quyidagi ishlarni qiladi:
   - Kodni yuklaydi
   - Pythonni sozlaydi
   - Kutubxonalarni o‘rnatadi
   - Testlarni bajaradi
   - (Agar sozlangan bo‘lsa) — deploy bosqichiga o‘tadi

---

## ✅ Xulosa:

- CI/CD orqali ish samaradorligi oshadi
- GitHub Actions — Django loyihalari uchun qulay va bepul vosita
- `.yml` fayllarni to‘g‘ri tuzish — avtomatlashtirishning asosiy kalitidir

---

## 🔗 Qo‘shimcha:

- [GitHub Actions hujjatlari](https://docs.github.com/en/actions)
- [Render.com API hujjatlari](https://render.com/docs)

---

**Keyingi dars:**
12-dars: GitHub Actions orqali Render serverga avtomatik deploy
