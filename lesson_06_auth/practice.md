# 🧪 Amaliyot: Django Forms va User Management

---

Ushbu amaliy mashqlar orqali siz Django formalarini yaratish, foydalanuvchi bilan ishlash va validatsiya jarayonlarini mustahkamlaysiz.

---

## ✅ 1. Form yaratish: `ContactForm`

### 🎯 Vazifa:

Yangi forma yarating: `ContactForm`, quyidagi maydonlar bilan:

* `name` — CharField
* `email` — EmailField
* `message` — Textarea

### 📄 Kod:

```python
# forms.py
tfrom django import forms

class ContactForm(forms.Form):
    name = forms.CharField(label="Ismingiz", max_length=100)
    email = forms.EmailField(label="Email manzil")
    message = forms.CharField(label="Xabar", widget=forms.Textarea)
```

---

## ✅ 2. Formani `views.py` orqali ko‘rsatish

### 🎯 Vazifa:

Formani foydalanuvchiga ko‘rsatish va POST orqali yuborilgan bo‘lsa, ma’lumotlarini konsolga chiqaring.

### 📄 Kod:

```python
# views.py
from django.shortcuts import render
from .forms import ContactForm

def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            print(form.cleaned_data)
    else:
        form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

---

## ✅ 3. `contact.html` template yozish

### 🎯 Vazifa:

Formani HTML sahifada ko‘rsating.

### 📄 Kod:

```html
<!-- templates/contact.html -->
<h2>Biz bilan bog‘laning</h2>
<form method="post">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Yuborish</button>
</form>
```

---

## ✅ 4. Foydalanuvchi Ro‘yxatdan o‘tishi (`UserCreationForm`)

### 🎯 Vazifa:

Django’ning tayyor formasi orqali yangi foydalanuvchi ro‘yxatdan o‘tsin.

### 📄 Kod:

```python
# views.pyrom django.contrib.auth.forms import UserCreationForm
from django.shortcuts import redirect

def register_view(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('login')
    else:
        form = UserCreationForm()
    return render(request, 'register.html', {'form': form})
```

---

## ✅ 5. Foydalanuvchi Ma’lumotlarini Yangilash (`UserChangeForm`)

### 🎯 Vazifa:

Foydalanuvchi o‘z profilini o‘zgartirishi uchun forma tayyorlang.

### 📄 Kod:

```python
# views.py
from django.contrib.auth.forms import UserChangeForm
from django.contrib.auth.decorators import login_required

@login_required
def profile_edit_view(request):
    if request.method == 'POST':
        form = UserChangeForm(request.POST, instance=request.user)
        if form.is_valid():
            form.save()
            return redirect('profile')
    else:
        form = UserChangeForm(instance=request.user)
    return render(request, 'edit_profile.html', {'form': form})
```

---

## 🧠 Tavsiyalar:

* Formani `ModelForm` bilan yaratish — formalarni DRY printsipiga asoslangan holatda yaratishga imkon beradi.
* Har doim `form.is_valid()` bilan validatsiya qiling.
* `form.cleaned_data` orqali foydalanuvchining kiritgan tozalangan ma’lumotlariga ega bo‘lasiz.
* Foydalanuvchi bilan ishlaganda `UserCreationForm`, `UserChangeForm`, `AuthenticationForm` dan foydalaning.
* Django formalar orqali har qanday ma’lumotni xavfsiz tarzda qabul qilish mumkin.

---

## 🔄 Keyingi amaliyot: `lesson_07_auth` — Login, Logout va Avtorizatsiya

👉 Keyingi darsda `login`, `logout`, `@login_required`, `permission`lar, foydalanuvchini tekshirish va foydalanuvchi rollarini boshqarishni o‘rganamiz.
