# 📘 Lesson 08: Django — Media Fayllar bilan Ishlash

---

## 🎯 Maqsad:

Djangoda foydalanuvchidan yuklangan media fayllarni (rasmlar, hujjatlar) qanday qabul qilish, saqlash va ko‘rsatishni o‘rganish.

---

## 📚 1. MEDIA va STATIC o‘rtasidagi farq

| Tur      | Ma'no                                                  |
| -------- | ------------------------------------------------------ |
| `STATIC` | Saytga tegishli o‘zgarmas fayllar: CSS, JS, shriftlar  |
| `MEDIA`  | Foydalanuvchi tomonidan yuklangan fayllar: rasm, video |

---

## ⚙️ 2. MEDIA sozlamalarini `settings.py` faylida belgilash

```python
# settings.py
import os

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

Bu bilan:

* MEDIA fayllar uchun URL prefiks: `/media/`
* Fayllar fizik jihatdan saqlanadigan joy: `media/` papkasi

---

## 🧩 3. `urls.py` faylida MEDIA sozlamalari

```python
# myproject/urls.py
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    ...
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

> Bu `DEBUG=True` bo‘lsa, server media fayllarni to‘g‘ridan-to‘g‘ri xizmat qilishi uchun.

---

## 📝 4. Modelga fayl qabul qiladigan maydon (`models.py`)

```python
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=255)
    content = models.TextField()
    image = models.ImageField(upload_to='article_images/')

    def __str__(self):
        return self.title
```

* `upload_to='article_images/'` — yuklangan fayllar shu papkaga saqlanadi: `media/article_images/`

---

## 🧾 5. Fayl qabul qiluvchi forma (`forms.py`)

```python
from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ['title', 'content', 'image']
```

---

## 🌐 6. `views.py` — faylni saqlash va ko‘rish

```python
from django.shortcuts import render, redirect
from .forms import ArticleForm
from .models import Article

def create_article(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('article_list')
    else:
        form = ArticleForm()
    return render(request, 'create_article.html', {'form': form})

def article_list(request):
    articles = Article.objects.all()
    return render(request, 'article_list.html', {'articles': articles})
```

> `request.FILES` media fayllarni qabul qilish uchun **majburiy**!

---

## 🧱 7. Shablon fayllari (HTML)

### `create_article.html`

```html
<h1>Yangi maqola</h1>
<form method="POST" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Saqlash</button>
</form>
```

> `enctype="multipart/form-data"` — fayl yuborish uchun **shartli** atribut!

### `article_list.html`

```html
<h1>Maqolalar</h1>
{% for article in articles %}
    <div>
        <h2>{{ article.title }}</h2>
        <img src="{{ article.image.url }}" width="200">
        <p>{{ article.content }}</p>
    </div>
{% endfor %}
```

---

## 🧪 8. Muhim eslatmalar

* Media fayllarni ko‘rish uchun: `DEBUG=True` holatda `MEDIA_URL` orqali xizmat qilinadi
* Fayl maydoni `ImageField` bo‘lsa, `Pillow` kutubxonasi kerak:

```bash
pip install Pillow
```

---

## ✅ Yakuniy xulosa:

| Tarkib             | Ko‘rinishi                         |
| ------------------ | ---------------------------------- |
| `media/`           | Saqlanayotgan fayllar papkasi      |
| `MEDIA_URL`        | `/media/` — URL orqali murojaat    |
| `request.FILES`    | Fayl qabul qilish uchun kerak      |
| `ImageField`       | Rasm/Media fayl maydoni modelda    |
| `enctype` atributi | Formada fayl jo‘natish uchun zarur |

---

**Keyingi dars:**
09-dars: Django REST API
