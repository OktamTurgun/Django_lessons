# 🧪 Amaliy Mashqlar — Django ORM Filtering

Bu mashqlar orqali `filter()`, `exclude()`, `get()`, `Q`, `F`, `annotate()`, `order_by()` kabi eng ko‘p ishlatiladigan Django ORM metodlarini real misollarda mustahkamlaysiz.

---

## 1️⃣ Dastlabki tayyorgarlik

### Modelni yaratish

```python
# models.py
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)
    birth_year = models.IntegerField()

    def __str__(self):
        return self.name


class Book(models.Model):
    title = models.CharField(max_length=150)
    published_year = models.IntegerField()
    price = models.DecimalField(max_digits=6, decimal_places=2)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

    def __str__(self):
        return self.title
```

### Admin va Superuser

```bash
python manage.py createsuperuser
```

```python
# admin.py
from .models import Author, Book

admin.site.register(Author)
admin.site.register(Book)
```

---

## 2️⃣ Ma’lumotlar kiritish

Admin paneldan yoki `shell` orqali quyidagi yozuvlarni kiriting:

```python
# shell orqali:
from books.models import Author, Book

a1 = Author.objects.create(name="Erkin Vohidov", birth_year=1936)
a2 = Author.objects.create(name="Abdulla Qodiriy", birth_year=1894)
a3 = Author.objects.create(name="O'tkir Hoshimov", birth_year=1941)

Book.objects.create(title="Tong nafasi", published_year=1975, price=45.0, author=a1)
Book.objects.create(title="Mehrobdan chayon", published_year=1929, price=35.0, author=a2)
Book.objects.create(title="Dunyoning ishlari", published_year=2000, price=50.0, author=a3)
Book.objects.create(title="Ikki eshik orasi", published_year=1980, price=40.0, author=a3)
Book.objects.create(title="Jannat iztirobi", published_year=1990, price=25.0, author=a1)
```

---

## 3️⃣ Amaliy filtering mashqlari

### 🔍 1. Narxi 40 dan katta kitoblarni toping:

```python
Book.objects.filter(price__gt=40)
```

---

### 🔍 2. 1990-yildan keyin chop etilgan kitoblar:

```python
Book.objects.filter(published_year__gt=1990)
```

---

### 🔍 3. Muallifi "O'tkir Hoshimov" bo‘lgan kitoblar:

```python
Book.objects.filter(author__name="O'tkir Hoshimov")
```

---

### 🔍 4. 30,000 so‘mdan arzon va 1980-yilgacha chiqqan kitoblar:

```python
Book.objects.filter(price__lt=30, published_year__lte=1980)
```

---

### 🔍 5. `Q()` orqali 2ta shart: narxi > 40 yoki 2000 yilda chiqqan:

```python
from django.db.models import Q

Book.objects.filter(Q(price__gt=40) | Q(published_year=2000))
```

---

### 🔍 6. `F()` orqali narxga nisbatan yil solishtirish (noto‘g‘ri mantiqiy test, faqat o‘rganish uchun):

```python
from django.db.models import F

Book.objects.filter(price__gt=F('published_year') / 50)  # faqat mashq
```

---

### 🔍 7. Narxi kamayish tartibida saralash:

```python
Book.objects.order_by('-price')
```

---

### 🔍 8. Mualliflar bo‘yicha kitoblar sonini hisoblash:

```python
from django.db.models import Count

Author.objects.annotate(book_count=Count('book'))
```

---

### 🔍 9. Kitoblar ichida eng qimmatini toping:

```python
Book.objects.order_by('-price').first()
```

---

### 🔍 10. `exclude()` — Narxi 30 dan kichiklarini chiqarib tashlang:

```python
Book.objects.exclude(price__lt=30)
```

---

## 🧠 Mustaqil mashq

Quyidagi shartlarga javob beradigan `QuerySet`lar yozing:

1. 1980-yilda chop etilgan kitoblar
2. Narxi 25 dan katta, lekin 50 dan kichik kitoblar
3. "Erkin" so‘zi bor mualliflar kitoblari
4. Kitob nomi `ch` harflari bilan boshlanadiganlar
5. 2 tadan ko‘p kitobi bor mualliflar

---

## ✅ Yakuniy maslahat

`filter()`, `exclude()`, `Q`, `F`, `annotate()` va `order_by()` usullarini bir necha bor takrorlab bajarish orqali Django ORM kuchli tomonini chuqur tushunasiz. Mashqlarni `shell`, `views.py` yoki `api` orqali bajaring.

---

**Keyingi dars**: `select_related`, `prefetch_related` bilan ishlash va performansni oshirish
