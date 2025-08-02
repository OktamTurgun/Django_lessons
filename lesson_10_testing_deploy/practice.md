# 🧪 Amaliyot: Django Loyihasini Test qilish va Deploy qilish

---

## 📁 1. Django Test yozish — `unittest` yordamida

### 🧪 1.1. Model testini yozish

**`models.py`**:

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=8, decimal_places=2)

    def __str__(self):
        return self.name
```

**`tests.py`**:

```python
from django.test import TestCase
from .models import Product

class ProductModelTest(TestCase):
    def test_product_str(self):
        product = Product(name="Test Mahsulot", price=99.99)
        self.assertEqual(str(product), "Test Mahsulot")
```

### ✅ Natija:

```bash
python manage.py test
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
```

---

## ✅ 2. Django view’larni test qilish

**`views.py`**:

```python
from django.http import HttpResponse

def hello_view(request):
    return HttpResponse("Salom, Dunyo!")
```

**`urls.py`**:

```python
from django.urls import path
from .views import hello_view

urlpatterns = [
    path('hello/', hello_view, name='hello'),
]
```

**`tests.py`**:

```python
from django.test import TestCase
from django.urls import reverse

class HelloViewTest(TestCase):
    def test_hello_view_returns_200(self):
        response = self.client.get('/hello/')
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "Salom, Dunyo!")
```

---

## 🚀 3. Render yordamida Django loyihasini deploy qilish

1. **GitHub repositoriyaga loyihani joylashtiring**:

```bash
git init
git add .
git commit -m "Initial commit for deployment"
git remote add origin https://github.com/username/repo.git
git push -u origin main
```

2. **Render.com saytiga kiring**, yangi **Web Service** yarating.

3. Quyidagi sozlamalarni to‘ldiring:

- **Name**: `django-deploy`
- **Build Command**: `pip install -r requirements.txt`
- **Start Command**: `gunicorn projectname.wsgi`
- **Python version**: 3.11 yoki mos versiya

4. `requirements.txt` fayliga quyidagilarni qo‘shing:

```
Django>=5.0.0
gunicorn
psycopg2-binary
```

5. `.render.yaml` sozlamasi (ixtiyoriy):

```yaml
services:
  - type: web
    name: django-app
    env: python
    buildCommand: "pip install -r requirements.txt"
    startCommand: "gunicorn projectname.wsgi"
```

---

## ℹ️ Tavsiyalar:

- Har bir test alohida faylda bo‘lishi yaxshi amaliyot.
- Deploymentdan avval `DEBUG=False` holatiga o‘tkazing.
- `.env` fayldan foydalaning va `SECRET_KEY`ni yashiring.
- `ALLOWED_HOSTS`da `render.com` subdomenini qo‘shishni unutmang.

---

✅ **Topshiriq:**

1. Model va view’laringiz uchun 2 ta test yozing.
2. Loyihangizni GitHubga push qiling.
3. Render.com’da birinchi marta deploy qilib ko‘ring.
