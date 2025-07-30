# 🔐 Dars 07: Autentifikatsiya va Avtorizatsiya (Authentication & Authorization)

---

## 📚 Mavzuga kirish

Bu darsda siz Django’da foydalanuvchilarni ro‘yxatdan o‘tkazish, tizimga kirish (login), tizimdan chiqish (logout), va ruxsatlarni (permissions) boshqarishni o‘rganasiz. Django’ning `auth` moduli orqali autentifikatsiya va avtorizatsiya jarayonlarini qanday amalga oshirish mumkinligini bosqichma-bosqich ko‘rib chiqamiz.

---

## 🎯 Dars maqsadlari:

* Django’da autentifikatsiya tizimi bilan tanishish
* Login/Logout jarayonlarini yaratish
* `@login_required` dekoratori bilan sahifalarni himoyalash
* Foydalanuvchi rollari va ruxsatlarni boshqarish

---

## 🧱 Django `auth` moduli haqida

Django avtomatik tarzda `django.contrib.auth` modulini taqdim etadi. U quyidagi imkoniyatlarni beradi:

* `User` modeli
* Parolni hashlash va tekshirish
* Login/Logout
* `is_authenticated`, `is_staff`, `is_superuser` atributlari
* `Permission` modeli

---

## 🛠️ 1. Login sahifasini yaratish

### 🔧 `urls.py`

```python
from django.urls import path
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('login/', auth_views.LoginView.as_view(template_name='login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(), name='logout'),
]
```

### 🧾 `login.html`

```html
<form method="post">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Login</button>
</form>
```

### 📌 Natija:

* Foydalanuvchi login sahifasiga kiradi
* Tizimga muvaffaqiyatli kirganidan so‘ng, u bosh sahifaga yo‘naltiriladi

---

## 🔒 2. `@login_required` bilan sahifani himoyalash

### views.py

```python
from django.contrib.auth.decorators import login_required

@login_required
def dashboard(request):
    return render(request, 'dashboard.html')
```

### settings.py

```python
LOGIN_URL = '/login/'
LOGIN_REDIRECT_URL = '/dashboard/'
LOGOUT_REDIRECT_URL = '/login/'
```

---

## 👥 3. Ruxsatlar (Permissions) bilan ishlash

Django foydalanuvchiga ruxsatlar berish imkonini beradi:

```python
from django.contrib.auth.models import Permission
from django.contrib.contenttypes.models import ContentType
from django.contrib.auth.models import User

user = User.objects.get(username='testuser')
permission = Permission.objects.get(codename='add_post')
user.user_permissions.add(permission)
```

### `has_perm` orqali tekshirish:

```python
if request.user.has_perm('blog.add_post'):
    # Kod bajariladi
```

---

## 🧩 4. Guruhlar (Groups) orqali rollarni boshqarish

```python
from django.contrib.auth.models import Group

# Guruh yaratish
authors = Group.objects.create(name='Author')

# Foydalanuvchini guruhga qo‘shish
user.groups.add(authors)

# Tekshirish
if user.groups.filter(name='Author').exists():
    # Ruxsat beriladi
```

---

## ✅ Yakuniy maslahatlar:

* Django `auth` moduli oddiy va ishonchli autentifikatsiya tizimini taqdim etadi.
* Har bir sahifaga ruxsat berishda `@login_required`, `user.has_perm()` va `user.groups` dan foydalaning.
* Har bir foydalanuvchi uchun kerakli redirect URL larni aniqlang.

---

## 🔗 Keyingi dars:

📄 `lesson_08_media` — Media fayllar bilan ishlash

---
