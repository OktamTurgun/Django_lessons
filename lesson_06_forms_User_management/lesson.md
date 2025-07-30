# 📝 6-dars: Django Forms va User Management

---

## 1. Django Forms nima?

* Django'da forma HTML forma o‘rnini bosadi, ma’lumotlarni qabul qilish, tekshirish va qayta ishlash uchun ishlatiladi.
* Python kod orqali forma yaratish, unga mos HTML hosil qilish va validatsiya qilishni soddalashtiradi.

---

## 2. Form yaratish: `forms.Form`

**forms.py:**

```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
```

**views.py:**

```python
from django.shortcuts import render
from .forms import ContactForm

def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # Ma'lumotlar toza
            print(form.cleaned_data)
    else:
        form = ContactForm()

    return render(request, 'contact.html', {'form': form})
```

**contact.html:**

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Yuborish</button>
</form>
```

---

## 3. ModelForm: `forms.ModelForm`

* `ModelForm` — modelga asoslangan forma yaratish imkonini beradi.

**forms.py:**

```python
from django import forms
from .models import Post

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content']
```

**view\.py:**

```python
from .forms import PostForm

def create_post(request):
    if request.method == 'POST':
        form = PostForm(request.POST)
        if form.is_valid():
            form.save()
    else:
        form = PostForm()
    return render(request, 'create_post.html', {'form': form})
```

---

## 4. Forma validatsiyasi

**Alohida maydon uchun validatsiya:**

```python
class AgeForm(forms.Form):
    age = forms.IntegerField()

    def clean_age(self):
        age = self.cleaned_data['age']
        if age < 18:
            raise forms.ValidationError("Yosh 18 dan kichik bo'lmasligi kerak!")
        return age
```

**Barcha forma uchun umumiy validatsiya:**

```python
    def clean(self):
        cleaned_data = super().clean()
        # Umumiy tekshiruvlar
        return cleaned_data
```

---

## 5. Tayyor formalar: UserCreationForm, AuthenticationForm

**Ro'yxatdan o'tish:**

```python
from django.contrib.auth.forms import UserCreationForm

def register_view(request):
    form = UserCreationForm(request.POST or None)
    if form.is_valid():
        form.save()
    return render(request, 'register.html', {'form': form})
```

**register.html:**

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Ro'yxatdan o'tish</button>
</form>
```

**Login qilish:**

```python
from django.contrib.auth.forms import AuthenticationForm
from django.contrib.auth import login

def login_view(request):
    form = AuthenticationForm(request, data=request.POST or None)
    if form.is_valid():
        user = form.get_user()
        login(request, user)
    return render(request, 'login.html', {'form': form})
```

---

## 6. Logout va Foydalanuvchini chiqarish

```python
from django.contrib.auth import logout
from django.shortcuts import redirect

def logout_view(request):
    logout(request)
    return redirect('login')
```

---

## 7. Maxfiy sahifalarni himoyalash

**1. LoginRequiredMixin (class-based view):**

```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import TemplateView

class DashboardView(LoginRequiredMixin, TemplateView):
    template_name = 'dashboard.html'
```

**2. @login\_required (function-based view):**

```python
from django.contrib.auth.decorators import login_required

@login_required
def dashboard_view(request):
    return render(request, 'dashboard.html')
```

---

## 8. Custom foydalanuvchi formasi

**forms.py:**

```python
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User
from django import forms

class CustomUserCreationForm(UserCreationForm):
    email = forms.EmailField()

    class Meta:
        model = User
        fields = ['username', 'email', 'password1', 'password2']
```

**views.py:**

```python
def register_view(request):
    form = CustomUserCreationForm(request.POST or None)
    if form.is_valid():
        form.save()
    return render(request, 'register.html', {'form': form})
```

---

## 9. Avtomatik auth URL'lar

```python
# urls.py
from django.urls import path, include

urlpatterns = [
    path('accounts/', include('django.contrib.auth.urls')),
]
```

Bu orqali siz avtomatik:

* `/accounts/login/`
* `/accounts/logout/`
* `/accounts/password_change/`

va boshqa auth URL'larini olasiz.

---

## 10. Foydali maslahatlar

* Har bir forma uchun `csrf_token` qo‘shishni unutmang
* Formalarni `crispy_forms` bilan chiroyli ko‘rsatish mumkin
* Formaga Bootstrap classlarini `widgets` orqali qo‘shing
* Har bir forma uchun alohida HTML template yozing
* Maxfiy sahifalarni `@login_required` orqali himoyalang

---

**Keyingi dars:**
07-dars: Autentifikatsiya va avtorizatsiya

