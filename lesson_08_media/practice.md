# 🧪 Amaliyot: Media fayllar bilan ishlash (lesson_08)

---

## 🎯 Maqsad

Django’da media fayllarni (rasmlar, hujjatlar) yuklash, saqlash va ko‘rsatishni o‘rganish. `MEDIA_ROOT`, `MEDIA_URL` tushunchalari bilan ishlash.

---

## ⚙️ 1. Media sozlamalarini qo‘shish

### `settings.py` faylida:

```python
import os

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

### `urls.py` faylida quyidagilarni qo‘shing:

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # boshqa url lar
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

## 🧩 2. Rasm yuklash formasi

### `models.py`:

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    image = models.ImageField(upload_to='products/')
```

### `forms.py`:

```python
from django import forms
from .models import Product

class ProductForm(forms.ModelForm):
    class Meta:
        model = Product
        fields = ['name', 'image']
```

---

## 🧪 3. Rasmni yuklash va ko‘rsatish

### `views.py`:

```python
from django.shortcuts import render, redirect
from .forms import ProductForm

def upload_product(request):
    if request.method == 'POST':
        form = ProductForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('products')
    else:
        form = ProductForm()
    return render(request, 'upload_product.html', {'form': form})
```

### `upload_product.html`:

```html
<h2>Rasm yuklash</h2>
<form method="post" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Yuklash</button>
</form>
```

---

## 🖼 4. Yuklangan rasmni ko‘rsatish

```python
# views.py
from .models import Product

def products(request):
    items = Product.objects.all()
    return render(request, 'products.html', {'items': items})
```

```html
<!-- templates/products.html -->
<h2>Yuklangan mahsulotlar</h2>
{% for item in items %}
  <div>
    <h4>{{ item.name }}</h4>
    <img src="{{ item.image.url }}" width="200" />
  </div>
{% endfor %}
```

---

## ✅ Sinov uchun:

- Turli rasmlarni yuklab ko‘ring
- Faqat rasm fayllarini qabul qilishni tekshirib ko‘ring
- `upload_to` parametrini o‘zgartirib, qaysi papkaga saqlanishini nazorat qiling

---

📚 Keyingi dars: **Django REST API**