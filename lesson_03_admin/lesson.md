# 📝 3-dars: Django Admin Panel va Superuser bilan ishlash

---

## 1. Django Admin Panel nima?
- **Django Admin Panel** — Django loyihangizda ma'lumotlarni vizual boshqarish uchun avtomatik yaratilgan web interfeys.
- Ma'lumotlar bazasidagi ma'lumotlarni qo'shish, o'zgartirish, o'chirish va ko'rish imkonini beradi.
- Har bir model uchun avtomatik forma va ro'yxat yaratadi.
- Batafsil: [Django Admin documentation](https://docs.djangoproject.com/en/stable/ref/contrib/admin/)

**Admin panelning afzalliklari:**
- Ma'lumotlarni tez va oson boshqarish
- Foydalanuvchi do'stona interfeys
- Xavfsizlik va ruxsatlar boshqaruvi
- Ma'lumotlarni filtrlash va qidirish imkoniyati

---

## 2. Superuser yaratish
Admin panelga kirish uchun superuser (administrator) yaratish kerak:

```bash
python manage.py createsuperuser
```

**Misol:**
```bash
Username: admin
Email address: admin@example.com
Password: ********
Password (again): ********
Superuser created successfully.
```

**Izohlar:**
- Username va parolni eslab qoling
- Email ixtiyoriy, lekin tavsiya etiladi
- Parol ekranda ko'rinmaydi (xavfsizlik uchun)
- Superuser barcha huquqlarga ega bo'ladi

---

## 3. Admin panelga kirish
1. Server ishga tushiring:
   ```bash
   python manage.py runserver
   ```
2. Brauzerda quyidagi manzilga kiring:
   ```
   http://127.0.0.1:8000/admin/
   ```
3. Superuser ma'lumotlari bilan tizimga kiring

**Admin panel ko'rinishi:**
- Chap tomonda barcha app va modellar ro'yxati
- Har bir model uchun "Add" va "Change" tugmalari
- Ma'lumotlarni qidirish va filtrlash imkoniyati

---

## 4. Modelni Admin panelga qo'shish
Modelni admin panelda ko'rish uchun uni ro'yxatdan o'tkazish kerak.

**1-qadam:** `blog/admin.py` faylini oching va quyidagi kodni yozing:

```python
from django.contrib import admin
from .models import Post

# Oddiy ro'yxatdan o'tkazish
admin.site.register(Post)
```

**2-qadam:** Server qayta ishga tushiring va admin panelga kiring.

**Natija:** Admin panelda "Posts" bo'limi paydo bo'ladi.

---

## 5. Admin panelni sozlash
Admin panelni yanada yaxshiroq ko'rinishga ega qilish uchun maxsus klass yaratish mumkin:

```python
from django.contrib import admin
from .models import Post

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    # Ro'yxatda ko'rsatiladigan maydonlar
    list_display = ['title', 'created_at', 'id']
    
    # Qidirish uchun maydonlar
    search_fields = ['title', 'content']
    
    # Filtrlash uchun maydonlar
    list_filter = ['created_at']
    
    # Har sahifada ko'rsatiladigan elementlar soni
    list_per_page = 10
    
    # Ro'yxatda tahrirlash imkoniyati
    list_editable = ['title']
    
    # Har bir element uchun ko'rsatiladigan maydonlar soni
    list_display_links = ['id']
    
    # Avtomatik to'ldirish
    prepopulated_fields = {'slug': ('title',)}
    
    # Maydonlarni guruhlash
    fieldsets = (
        ('Asosiy ma\'lumotlar', {
            'fields': ('title', 'content')
        }),
        ('Qo\'shimcha ma\'lumotlar', {
            'fields': ('created_at',),
            'classes': ('collapse',)
        }),
    )
```

**Sozlamalar tushuntirilishi:**
- `list_display` — ro'yxatda ko'rsatiladigan maydonlar
- `search_fields` — qidirish uchun ishlatiladigan maydonlar
- `list_filter` — filtrlash uchun maydonlar
- `list_per_page` — sahifada ko'rsatiladigan elementlar soni
- `list_editable` — ro'yxatda to'g'ridan-to'g'ri tahrirlash mumkin bo'lgan maydonlar
- `fieldsets` — maydonlarni guruhlash va yashirish

---

## 6. Qo'shimcha model misollari
Keling, blog uchun qo'shimcha modellar yaratamiz:

**1. Category modeli:**
```python
# blog/models.py
from django.db import models

class Category(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return self.name
    
    class Meta:
        verbose_name = "Kategoriya"
        verbose_name_plural = "Kategoriyalar"

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    is_published = models.BooleanField(default=False)
    
    def __str__(self):
        return self.title
    
    class Meta:
        verbose_name = "Post"
        verbose_name_plural = "Postlar"
        ordering = ['-created_at']
```

**2. Admin sozlamalari:**
```python
# blog/admin.py
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

**3. Migratsiya qilish:**
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 7. Admin panelda amallar
Admin panelda quyidagi amallarni bajarishingiz mumkin:

**Ma'lumot qo'shish:**
1. Model ro'yxatida "Add" tugmasini bosing
2. Kerakli maydonlarni to'ldiring
3. "Save" tugmasini bosing

**Ma'lumotni tahrirlash:**
1. Ro'yxatdan kerakli elementni tanlang
2. Maydonlarni o'zgartiring
3. "Save" tugmasini bosing

**Ma'lumotni o'chirish:**
1. Elementni tanlang
2. "Delete" tugmasini bosing
3. Tasdiqlang

**Bir nechta elementni tanlash:**
1. Checkbox orqali elementlarni tanlang
2. Yuqoridagi "Actions" ro'yxatidan amalni tanlang
3. "Go" tugmasini bosing

---

## 8. Xavfsizlik va ruxsatlar
Admin panelda foydalanuvchilar uchun turli darajadagi ruxsatlar berish mumkin:

**Staff user yaratish:**
```bash
python manage.py createsuperuser --username=editor --email=editor@example.com
```

**Kodda ruxsatlarni boshqarish:**
```python
# blog/admin.py
from django.contrib import admin
from django.contrib.auth.models import User, Group

class PostAdmin(admin.ModelAdmin):
    def has_add_permission(self, request):
        # Faqat superuser qo'sha oladi
        return request.user.is_superuser
    
    def has_change_permission(self, request, obj=None):
        # Barcha staff userlar o'zgartira oladi
        return request.user.is_staff
    
    def has_delete_permission(self, request, obj=None):
        # Faqat superuser o'chira oladi
        return request.user.is_superuser
```

---

## 9. Amaliyot uchun tavsiyalar
1. **O'zingizning modellarizni yarating:**
   - Book (kitob) modeli
   - Author (muallif) modeli
   - Publisher (nashriyot) modeli

2. **Admin panelni sozlang:**
   - Ro'yxatda ko'rsatiladigan maydonlarni tanlang
   - Qidirish va filtrlash imkoniyatlarini qo'shing
   - Maydonlarni guruhlang

3. **Ma'lumotlar bilan ishlang:**
   - Yangi ma'lumotlar qo'shing
   - Mavjud ma'lumotlarni tahrirlang
   - Ma'lumotlarni o'chiring

4. **Xavfsizlikni sinab ko'ring:**
   - Turli foydalanuvchilar yarating
   - Ruxsatlarni sinab ko'ring

---

## 10. Foydali maslahatlar
- **Backup yarating:** Muhim ma'lumotlarni yo'qotmaslik uchun muntazam backup qiling
- **Test muhitida ishlang:** Yangi o'zgarishlarni avval test muhitida sinab ko'ring
- **Hujjatlarni o'qing:** Django admin haqida batafsil ma'lumot uchun rasmiy hujjatlarni o'qing
- **Kodni tartibda saqlang:** Admin sozlamalarini tushunarli va tartibda yozing

---

**Keyingi dars:**  
4-dars: Django Views va URL Patterns  
Bu darsda siz Django views yaratish, URL patterns sozlash va web sahifalarini ko'rsatishni o'rganasiz. 