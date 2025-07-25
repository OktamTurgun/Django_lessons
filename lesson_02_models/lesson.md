# 📝 2-dars: Django Model va Ma'lumotlar Bazasi Asoslari

---

## 1. Model nima?
- **Model** — Django’da ma’lumotlar bazasidagi jadvalga mos keladigan Python klassi.
- Har bir model `models.Model` dan meros oladi.
- Model orqali ma’lumotlar bazasida jadval yaratiladi va unga ma’lumotlar yoziladi.
- Batafsil: [Django Models documentation](https://docs.djangoproject.com/en/stable/topics/db/models/)

**Misol:**
```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```

---

## 2. Model yaratish
1. Django loyihangizda yangi app yarating (masalan, `blog`):
   ```bash
   python manage.py startapp blog
   ```
   Bu buyruq `blog` nomli yangi papka va fayllar yaratadi.
2. App’ni `INSTALLED_APPS` ga qo‘shing (`mysite/settings.py`):
   ```python
   INSTALLED_APPS = [
       # ...
       'blog',
   ]
   ```
   Bu qadam app kodlari loyihaga ulanishi uchun zarur.
3. Model yozing (`blog/models.py`):
   ```python
   from django.db import models

   class Post(models.Model):
       title = models.CharField(max_length=100)
       content = models.TextField()
       created_at = models.DateTimeField(auto_now_add=True)
   ```
   - `CharField` — qisqa matn uchun (masalan, sarlavha)
   - `TextField` — uzun matn uchun (masalan, maqola matni)
   - `DateTimeField(auto_now_add=True)` — avtomatik sana va vaqt

---

## 3. Migratsiya va ma’lumotlar bazasi
- Model o‘zgarsa, migratsiya qilish kerak:
  ```bash
  python manage.py makemigrations
  python manage.py migrate
  ```
- `makemigrations` — modeldagi o‘zgarishlarni migratsiya fayliga yozadi.
- `migrate` — migratsiya faylini asosiy ma’lumotlar bazasiga qo‘llaydi.
- Batafsil: [Django Migrations documentation](https://docs.djangoproject.com/en/stable/topics/migrations/)

**Izoh:**
- Har safar modelga yangi maydon qo‘shsangiz yoki o‘zgartirsangiz, bu ikki buyruqni qayta ishlatishingiz kerak.

---

## 4. Django ORM asoslari
- Django ORM (Object-Relational Mapping) yordamida model orqali ma’lumotlar bazasi bilan ishlash mumkin:
- Batafsil: [Django ORM documentation](https://docs.djangoproject.com/en/stable/topics/db/queries/)

**Misollar:**
```python
# Yangi post qo‘shish
Post.objects.create(title="Salom", content="Django darsi!")

# Barcha postlarni olish
Post.objects.all()

# Bitta postni olish (id orqali)
Post.objects.get(id=1)

# Filtrlash (masalan, sarlavhasi 'Salom' bo'lgan postlar)
Post.objects.filter(title="Salom")

# Postni o‘chirish
Post.objects.get(id=1).delete()

# Postni yangilash
post = Post.objects.get(id=1)
post.title = "Yangi sarlavha"
post.save()
```

**Izoh:**
- ORM yordamida SQL yozmasdan, to‘g‘ridan-to‘g‘ri Python kodida ma’lumotlar bilan ishlaysiz.
- `objects` — modelning asosiy manager’i, barcha so‘rovlar shu orqali amalga oshiriladi.

---

## 5. Amaliyot uchun tavsiyalar
- O‘zingiz kichik model yozib, migratsiya qilib, ORM orqali ma’lumot qo‘shib, o‘chirib, o‘zgartirib ko‘ring.
- Admin panelga modelni qo‘shib, ma’lumotlarni vizual boshqarib ko‘ring (keyingi darsda ko‘rib chiqiladi).
- Rasmiy hujjat: [Django Model API reference](https://docs.djangoproject.com/en/stable/ref/models/)

---

**Keyingi dars:**  
3-dars: Django admin panel va superuser bilan ishlash  
Bu darsda siz modelni admin panelga qo‘shish, superuser yaratish va ma’lumotlarni vizual boshqarishni o‘rganasiz. 