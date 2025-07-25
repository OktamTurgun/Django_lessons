# 🧪 Amaliyot: Django Model va Ma'lumotlar Bazasi

---

## 1. Yangi app yarating
```bash
python manage.py startapp blog
```

## 2. App’ni settings.py ga qo‘shing
`mysite/settings.py` faylida:
```python
INSTALLED_APPS = [
    # ...
    'blog',
]
```

## 3. Model yozing
`blog/models.py` faylida quyidagicha model yozing:
```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```

## 4. Migratsiya qiling
```bash
python manage.py makemigrations
python manage.py migrate
```

## 5. Django shell orqali ma’lumot qo‘shing va oling
```bash
python manage.py shell
```
Shell oynasida:
```python
from blog.models import Post
Post.objects.create(title="Birinchi post", content="Django model amaliyoti!")
Post.objects.all()
```

---

✅ Natijani admin panelda ko‘rish uchun keyingi darsni kuting! 