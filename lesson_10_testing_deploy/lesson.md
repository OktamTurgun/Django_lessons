# 📘 Dars 10: Django’da Test va Deployment

---

## 🎯 Maqsad

Ushbu darsda siz quyidagilarni o‘rganasiz:

- Django test tizimini ishlatish
- Unit test yozish
- CI/CD haqida tushuncha olish
- Django loyihasini production serverga joylashtirish

---

## 🧪 1. Django Test Tizimi

Django testing uchun `unittest` moduliga asoslangan kuchli tizimni taqdim etadi.

### ✅ Test yozish uchun quyidagilar bajariladi:

1. `tests.py` yoki `tests/` papkasi yaratiladi.
2. `TestCase` sinfi orqali testlar yoziladi.
3. `python manage.py test` buyrug‘i orqali testlar ishga tushiriladi.

### Misol:

```python
# blog/tests.py
from django.test import TestCase
from .models import Post

class PostModelTest(TestCase):
    def test_str_method(self):
        post = Post(title="Test sarlavha")
        self.assertEqual(str(post), "Test sarlavha")
```

---

## 🧰 2. Test turi: Unit vs Integration

| Turi         | Tavsif |
|--------------|--------|
| Unit Test    | Model, View, Form kabi kichik qismlar test qilinadi |
| Integration  | Bir nechta qismlar birga ishlaganda tekshiriladi    |

---

## ⚙️ 3. Coverage bilan test qamrovini aniqlash

```bash
pip install coverage

coverage run manage.py test
coverage report
coverage html  # HTML ko‘rinishda natijalarni ko‘rish uchun
```

---

## 🚀 4. Deployment haqida tushuncha

Deployment — bu ishlab chiqilgan loyihani **ishlaydigan serverga** (production) joylashtirish jarayonidir.

### Asosiy qadamlari:

1. Kodni tayyorlash (testdan o‘tkazish)
2. Serverga joylashtirish (GitHub, Render, Heroku)
3. Xavfsizlik sozlamalari (`DEBUG=False`, `ALLOWED_HOSTS`, `SECRET_KEY`)
4. Static fayllarni yig‘ish: `python manage.py collectstatic`
5. Ma’lumotlar bazasi migratsiyasi: `python manage.py migrate`

---

## 🌐 5. Render.com ga joylashtirish

Render - Django loyihalarini **bepul** joylashtirish mumkin bo‘lgan platforma.

### Qadamlar:

1. GitHub repo tayyorlash
2. `requirements.txt`, `gunicorn`, `Procfile` tayyorlash
3. Render’da yangi Web Service yaratish
4. Environment Variables (`SECRET_KEY`, `DEBUG=False`) sozlash

---

## 📦 6. .env fayl bilan maxfiy sozlamalarni ajratish

```env
# .env
SECRET_KEY='super-secret-key'
DEBUG=False
ALLOWED_HOSTS=yourdomain.com
```

**`python-decouple`** kutubxonasi orqali bu sozlamalarni alohida boshqarish:

```bash
pip install python-decouple
```

```python
# settings.py
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', cast=bool)
```

---

## ✅ Yakuniy maslahatlar

- Har bir `view`, `model`, `form` uchun alohida test yozing.
- Kodni `DEBUG=False` holatda sinab ko‘ring.
- Deploymentdan oldin `collectstatic` va `migrate` buyrug‘ini bajaring.
- Har bir sozlamani `.env` fayl orqali boshqaring.

---

🚀 **Keyingi dars:** Deployment amaliyoti — `practice.md` faylida!