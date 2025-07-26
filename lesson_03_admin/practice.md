# 🛠️ 3-dars: Django Admin Panel - Amaliyot

---

## 📋 Mashg'ulot 1: Superuser yaratish va admin panelga kirish

### Vazifa
Django loyihangizda superuser yarating va admin panelga kirib ko'ring.

### Qadamlar
1. **Superuser yarating:**
   ```bash
   python manage.py createsuperuser
   ```
   - Username: `admin`
   - Email: `admin@example.com`
   - Password: `admin123`

2. **Server ishga tushiring:**
   ```bash
   python manage.py runserver
   ```

3. **Admin panelga kiring:**
   - Brauzerda: `http://127.0.0.1:8000/admin/`
   - Yuqoridagi ma'lumotlar bilan tizimga kiring

### Natija
Admin panel ochiladi va siz barcha Django modellarini ko'rasiz.

---

## 📋 Mashg'ulot 2: Blog app yaratish va model qo'shish

### Vazifa
Blog app yarating va Post modelini admin panelga qo'shing.

### Qadamlar

**1. Blog app yarating:**
```bash
python manage.py startapp blog
```

**2. App'ni ro'yxatdan o'tkazing (`mysite/settings.py`):**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',  # Qo'shing
]
```

**3. Post modelini yarating (`blog/models.py`):**
```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200, verbose_name="Sarlavha")
    content = models.TextField(verbose_name="Matn")
    created_at = models.DateTimeField(auto_now_add=True, verbose_name="Yaratilgan sana")
    updated_at = models.DateTimeField(auto_now=True, verbose_name="Yangilangan sana")
    is_published = models.BooleanField(default=False, verbose_name="Nashr etilgan")
    
    def __str__(self):
        return self.title
    
    class Meta:
        verbose_name = "Post"
        verbose_name_plural = "Postlar"
        ordering = ['-created_at']
```

**4. Modelni admin panelga qo'shing (`blog/admin.py`):**
```python
from django.contrib import admin
from .models import Post

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'created_at', 'is_published']
    list_filter = ['is_published', 'created_at']
    search_fields = ['title', 'content']
    list_editable = ['is_published']
    list_per_page = 10
```

**5. Migratsiya qiling:**
```bash
python manage.py makemigrations
python manage.py migrate
```

### Natija
Admin panelda "Posts" bo'limi paydo bo'ladi va siz postlar qo'sha olasiz.

---

## 📋 Mashg'ulot 3: Category modeli qo'shish

### Vazifa
Category modelini yarating va Post bilan bog'lang.

### Qadamlar

**1. Category modelini qo'shing (`blog/models.py`):**
```python
from django.db import models

class Category(models.Model):
    name = models.CharField(max_length=100, verbose_name="Nomi")
    description = models.TextField(blank=True, verbose_name="Tavsif")
    created_at = models.DateTimeField(auto_now_add=True, verbose_name="Yaratilgan sana")
    
    def __str__(self):
        return self.name
    
    class Meta:
        verbose_name = "Kategoriya"
        verbose_name_plural = "Kategoriyalar"

class Post(models.Model):
    title = models.CharField(max_length=200, verbose_name="Sarlavha")
    content = models.TextField(verbose_name="Matn")
    category = models.ForeignKey(Category, on_delete=models.CASCADE, verbose_name="Kategoriya")
    created_at = models.DateTimeField(auto_now_add=True, verbose_name="Yaratilgan sana")
    updated_at = models.DateTimeField(auto_now=True, verbose_name="Yangilangan sana")
    is_published = models.BooleanField(default=False, verbose_name="Nashr etilgan")
    
    def __str__(self):
        return self.title
    
    class Meta:
        verbose_name = "Post"
        verbose_name_plural = "Postlar"
        ordering = ['-created_at']
```

**2. Admin sozlamalarini yangilang (`blog/admin.py`):**
```python
from django.contrib import admin
from .models import Category, Post

@admin.register(Category)
class CategoryAdmin(admin.ModelAdmin):
    list_display = ['name', 'created_at']
    search_fields = ['name']
    list_per_page = 20

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'category', 'created_at', 'is_published']
    list_filter = ['category', 'is_published', 'created_at']
    search_fields = ['title', 'content']
    list_editable = ['is_published']
    list_per_page = 25
    
    fieldsets = (
        ('Asosiy ma\'lumotlar', {
            'fields': ('title', 'content', 'category')
        }),
        ('Holat', {
            'fields': ('is_published',)
        }),
        ('Vaqt', {
            'fields': ('created_at', 'updated_at'),
            'classes': ('collapse',)
        }),
    )
    
    readonly_fields = ['created_at', 'updated_at']
```

**3. Migratsiya qiling:**
```bash
python manage.py makemigrations
python manage.py migrate
```

### Natija
Admin panelda "Categories" va "Posts" bo'limlari paydo bo'ladi.

---

## 📋 Mashg'ulot 4: Ma'lumotlar bilan ishlash

### Vazifa
Admin panelda ma'lumotlar qo'shing, tahrirlang va o'chiring.

### Qadamlar

**1. Kategoriyalar qo'shing:**
- Admin panelda "Categories" bo'limiga kiring
- "Add Category" tugmasini bosing
- Quyidagi kategoriyalarni qo'shing:
  - **Nomi:** "Dasturlash"
  - **Tavsif:** "Dasturlash haqida maqolalar"
  
  - **Nomi:** "Texnologiya"
  - **Tavsif:** "Zamonaviy texnologiyalar"

**2. Postlar qo'shing:**
- "Posts" bo'limiga kiring
- "Add Post" tugmasini bosing
- Quyidagi postlarni qo'shing:

**Post 1:**
- **Sarlavha:** "Django Admin Panel haqida"
- **Matn:** "Django admin panel juda qulay va foydali vosita..."
- **Kategoriya:** "Dasturlash"
- **Nashr etilgan:** ✅

**Post 2:**
- **Sarlavha:** "Python dasturlash tili"
- **Matn:** "Python - dunyodagi eng mashhur dasturlash tillaridan biri..."
- **Kategoriya:** "Dasturlash"
- **Nashr etilgan:** ❌

**Post 3:**
- **Sarlavha:** "Sun'iy intellekt kelajagi"
- **Matn:** "AI texnologiyasi tez rivojlanmoqda..."
- **Kategoriya:** "Texnologiya"
- **Nashr etilgan:** ✅

**3. Ma'lumotlarni tahrirlang:**
- Post ro'yxatida "Django Admin Panel haqida" postini tanlang
- Matnni o'zgartiring
- "Save" tugmasini bosing

**4. Ma'lumotlarni o'chiring:**
- "Python dasturlash tili" postini tanlang
- "Delete" tugmasini bosing
- Tasdiqlang

### Natija
Siz admin panelda ma'lumotlar bilan to'liq ishlashni o'rgandiz.

---

## 📋 Mashg'ulot 5: Qo'shimcha admin sozlamalari

### Vazifa
Admin panelni yanada yaxshiroq ko'rinishga ega qiling.

### Qadamlar

**1. Admin sozlamalarini kengaytiring (`blog/admin.py`):**
```python
from django.contrib import admin
from .models import Category, Post

@admin.register(Category)
class CategoryAdmin(admin.ModelAdmin):
    list_display = ['name', 'description', 'created_at']
    search_fields = ['name', 'description']
    list_filter = ['created_at']
    list_per_page = 20
    
    fieldsets = (
        ('Asosiy ma\'lumotlar', {
            'fields': ('name', 'description')
        }),
        ('Vaqt', {
            'fields': ('created_at',),
            'classes': ('collapse',)
        }),
    )
    
    readonly_fields = ['created_at']

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'category', 'created_at', 'is_published', 'get_word_count']
    list_filter = ['category', 'is_published', 'created_at']
    search_fields = ['title', 'content']
    list_editable = ['is_published']
    list_per_page = 25
    
    fieldsets = (
        ('Asosiy ma\'lumotlar', {
            'fields': ('title', 'content', 'category')
        }),
        ('Holat', {
            'fields': ('is_published',)
        }),
        ('Vaqt', {
            'fields': ('created_at', 'updated_at'),
            'classes': ('collapse',)
        }),
    )
    
    readonly_fields = ['created_at', 'updated_at']
    
    def get_word_count(self, obj):
        return len(obj.content.split())
    get_word_count.short_description = "So'zlar soni"
    
    actions = ['make_published', 'make_unpublished']
    
    def make_published(self, request, queryset):
        updated = queryset.update(is_published=True)
        self.message_user(request, f'{updated} ta post nashr etildi.')
    make_published.short_description = "Tanlangan postlarni nashr etish"
    
    def make_unpublished(self, request, queryset):
        updated = queryset.update(is_published=False)
        self.message_user(request, f'{updated} ta post nashrdan olindi.')
    make_unpublished.short_description = "Tanlangan postlarni nashrdan olish"
```

**2. Server qayta ishga tushiring va admin panelga kiring**

### Natija
Admin panelda qo'shimcha funksiyalar paydo bo'ladi:
- So'zlar soni ko'rsatiladi
- Bir nechta postlarni tanlab nashr etish/olish mumkin
- Qidirish va filtrlash yaxshilandi

---

## 📋 Mashg'ulot 6: Xavfsizlik va ruxsatlar

### Vazifa
Turli darajadagi foydalanuvchilar yarating va ularning ruxsatlarini sinab ko'ring.

### Qadamlar

**1. Editor foydalanuvchisi yarating:**
```bash
python manage.py createsuperuser --username=editor --email=editor@example.com
```
- Password: `editor123`

**2. Staff user yarating (admin panel orqali):**
- Admin panelga kiring
- "Users" bo'limiga kiring
- "Add User" tugmasini bosing
- Quyidagi ma'lumotlarni kiriting:
  - **Username:** `moderator`
  - **Email:** `moderator@example.com`
  - **Password:** `moderator123`
  - **Staff status:** ✅
  - **Superuser status:** ❌

**3. Ruxsatlarni sinab ko'ring:**
- Har bir foydalanuvchi bilan admin panelga kiring
- Qanday funksiyalarga ruxsat berilganini ko'ring

### Natija
Turli foydalanuvchilar turli huquqlarga ega ekanligini tushunasiz.

---

## 🎯 Qo'shimcha vazifalar

### Vazifa 1: Book store modeli
Kitob do'koni uchun modellar yarating:

```python
# books/models.py
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)
    bio = models.TextField(blank=True)
    birth_date = models.DateField(null=True, blank=True)
    
    def __str__(self):
        return self.name

class Publisher(models.Model):
    name = models.CharField(max_length=100)
    address = models.TextField()
    phone = models.CharField(max_length=20)
    
    def __str__(self):
        return self.name

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
    isbn = models.CharField(max_length=13, unique=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    published_date = models.DateField()
    is_available = models.BooleanField(default=True)
    
    def __str__(self):
        return self.title
```

### Vazifa 2: Admin sozlamalari
Book store uchun admin panel sozlang:

```python
# books/admin.py
from django.contrib import admin
from .models import Author, Publisher, Book

@admin.register(Author)
class AuthorAdmin(admin.ModelAdmin):
    list_display = ['name', 'birth_date']
    search_fields = ['name']
    list_filter = ['birth_date']

@admin.register(Publisher)
class PublisherAdmin(admin.ModelAdmin):
    list_display = ['name', 'phone']
    search_fields = ['name']

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    list_display = ['title', 'author', 'publisher', 'price', 'is_available']
    list_filter = ['author', 'publisher', 'is_available', 'published_date']
    search_fields = ['title', 'author__name', 'publisher__name']
    list_editable = ['price', 'is_available']
    list_per_page = 20
```

---

## ✅ Tekshirish ro'yxati

- [ ] Superuser yaratildi
- [ ] Admin panelga kirish mumkin
- [ ] Blog app yaratildi va sozlandi
- [ ] Post va Category modellari yaratildi
- [ ] Admin panelda modellar ko'rinadi
- [ ] Ma'lumotlar qo'shildi, tahrirlandi va o'chirildi
- [ ] Qo'shimcha admin sozlamalari qo'shildi
- [ ] Turli foydalanuvchilar yaratildi
- [ ] Xavfsizlik sinab ko'rildi

---

## 🎉 Natija

Siz Django admin panel bilan to'liq ishlashni o'rgandingiz:
- Superuser yaratish
- Modellarni admin panelga qo'shish
- Admin panelni sozlash
- Ma'lumotlar bilan ishlash
- Xavfsizlik va ruxsatlar

**Keyingi dars:** Django Views va URL Patterns 