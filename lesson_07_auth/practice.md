# 🧪 Amaliyot: Autentifikatsiya va Avtorizatsiya (lesson\_07\_auth)

---

## 🔐 1. Foydalanuvchi Ro‘yxatdan O‘tishi (Registration)

### 🎯 Vazifa:

Custom `UserCreationForm` yordamida ro‘yxatdan o‘tish formasi yarating.

### 🧱 Strukturasi:

`users/forms.py`:

```python
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User

class CustomUserCreationForm(UserCreationForm):
    class Meta:
        model = User
        fields = ('username', 'email', 'password1', 'password2')
```

`users/views.py`:

```python
from django.shortcuts import render, redirect
from .forms import CustomUserCreationForm

def register_view(request):
    if request.method == 'POST':
        form = CustomUserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('login')
    else:
        form = CustomUserCreationForm()
    return render(request, 'users/register.html', {'form': form})
```

### ✅ Natija:

Foydalanuvchi `username`, `email`, `parol` kiritib ro‘yxatdan o‘ta oladi.

---

## 🔑 2. Foydalanuvchi Tizimga Kirishi (Login)

### 🎯 Vazifa:

`LoginView` orqali login sahifasini sozlang.

### 🧱 Strukturasi:

`users/urls.py`:

```python
from django.contrib.auth.views import LoginView

urlpatterns = [
    path('login/', LoginView.as_view(template_name='users/login.html'), name='login'),
]
```

`login.html`:

```html
<form method="post">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Login</button>
</form>
```

### ✅ Natija:

Foydalanuvchi tizimga to‘g‘ri ma’lumotlar bilan kira oladi.

---

## 🔓 3. Tizimdan Chiqish (Logout)

### 🎯 Vazifa:

`LogoutView` bilan chiqish funksiyasi qo‘shing.

### 🧱 Strukturasi:

`users/urls.py`:

```python
from django.contrib.auth.views import LogoutView

urlpatterns += [
    path('logout/', LogoutView.as_view(), name='logout'),
]
```

`base.html`:

```html
{% if user.is_authenticated %}
    <a href="{% url 'logout' %}">Logout</a>
{% else %}
    <a href="{% url 'login' %}">Login</a>
{% endif %}
```

### ✅ Natija:

Foydalanuvchi tizimdan chiqishi bilan `session` tugaydi.

---

## 👮‍♂️ 4. Avtorizatsiya: Login Talab Qilinadigan Sahifa

### 🎯 Vazifa:

Ayrim sahifalarni faqat login bo‘lgan foydalanuvchilargagina ko‘rsating.

### 🧱 Strukturasi:

`views.py`:

```python
from django.contrib.auth.decorators import login_required

@login_required
def dashboard(request):
    return render(request, 'users/dashboard.html')
```

Yoki CBV (Class Based View) uchun:

```python
from django.contrib.auth.mixins import LoginRequiredMixin

class DashboardView(LoginRequiredMixin, TemplateView):
    template_name = 'users/dashboard.html'
```

### ✅ Natija:

Login qilmagan foydalanuvchi avtomatik tarzda login sahifasiga yo‘naltiriladi.

---

## 📌 5. Avtorizatsiya: Guruh va Ruxsatlar (Permissions)

### 🎯 Vazifa:

Admin panelda ruxsat sozlashni o‘rganing va `@permission_required` dekoratoridan foydalaning.

### 🧱 Strukturasi:

`views.py`:

```python
from django.contrib.auth.decorators import permission_required

@permission_required('blog.add_post')
def create_post(request):
    ...
```

Admin panelda `Permissions` bo‘limidan kerakli huquqni foydalanuvchiga biriktiring.

### ✅ Natija:

Faqat kerakli ruxsati bor foydalanuvchi sahifaga kira oladi.

---

## 📌 6. Foydalanuvchi Holatini Tekshirish

### 🎯 Vazifa:

Frontendda foydalanuvchi holatini ko‘rsatish.

### 🧱 Strukturasi:

`base.html`:

```html
{% if user.is_authenticated %}
    Salom, {{ user.username }}!
{% else %}
    Mehmon foydalanuvchi!
{% endif %}
```

---

## 💡 Tavsiyalar:

* Har bir `form` uchun validatsiyani unutmang.
* `LoginRequiredMixin` va `@login_required` ni aralashtirib ishlatmaslikka harakat qiling.
* Sahifa yo‘nalishlarini `LOGIN_URL` orqali aniqlang.
* `next` parametri orqali foydalanuvchini kirganidan so‘ng kerakli sahifaga qayta yo‘naltiring.

---

## 🔜 Keyingi dars: lesson\_08 — Media fayllar bilan ishlash
