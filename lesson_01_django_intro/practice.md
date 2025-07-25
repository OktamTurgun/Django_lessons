# 🧪 Amaliyot: Django Muhitini Tayyorlash va Loyihani Ishga Tushurish

---

## 1. Muhitni tayyorlash

### 1.1 Python o‘rnatilganini tekshirish

Terminalda quyidagilarni yozing:

```bash
python --version
```
Agar versiya chiqsa, Python o‘rnatilgan. Aks holda, [python.org](https://www.python.org/) saytidan yuklab oling.

### 1.2 pip o‘rnatilganini tekshirish

```bash
pip --version
```
Agar versiya chiqsa, pip o‘rnatilgan.

---

## 2. Virtual muhit yaratish

Loyihani alohida muhitda yuritish uchun virtual muhit yarating:

```bash
python -m venv env
```

### Muhitni faollashtirish

- **Windows:**
  ```bash
  env\Scripts\activate
  ```
- **Mac/Linux:**
  ```bash
  source env/bin/activate
  ```

Muhit faollashganidan so‘ng terminalda `(env)` yozuvi chiqadi.

---

## 3. Django framework’ini o‘rnatish

```bash
pip install django
```

O‘rnatilganini tekshirish:

```bash
django-admin --version
```

---

## 4. Yangi Django loyihasi yaratish

```bash
django-admin startproject mysite
cd mysite
```

Yangi loyiha tuzilmasi:

```
mysite/
├── manage.py
└── mysite/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    ├── asgi.py
    └── wsgi.py
```

---

## 5. Django serverini ishga tushirish

```bash
python manage.py runserver
```

Brauzerda oching:  
👉 http://127.0.0.1:8000/

Agar “The install worked successfully!” yozuvi chiqsa, Django muvaffaqiyatli ishlamoqda.

---

**Tabriklaymiz! Siz birinchi Django loyihangizni yaratdingiz va ishga tushirdingiz!**