# 🧪 12-DARS: Throttling — Amaliy Mashqlar

Ushbu darsda siz Django Rest Framework’da **Throttling** bo‘yicha amaliy ko‘nikmalar hosil qilasiz. Bu mashqlar orqali `AnonRateThrottle`, `UserRateThrottle` va custom throttle’lar bilan ishlashni puxta o‘zlashtirasiz.

---

## 1. 🔧 Loyiha va App yaratish

```bash
django-admin startproject throttling_project
cd throttling_project
python manage.py startapp api
```

`api` app’ni `settings.py`ga qo‘shing:

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'api',
]
```

---

## 2. 📁 Foydalanuvchi modeli va Serializer

`api/models.py`:

```python
from django.db import models

class Comment(models.Model):
    username = models.CharField(max_length=50)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.username} - {self.content[:30]}"
```

```bash
python manage.py makemigrations
python manage.py migrate
```

`api/serializers.py`:

```python
from rest_framework import serializers
from .models import Comment

class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = '__all__'
```

---

## 3. 🌐 View va URL sozlash

`api/views.py`:

```python
from rest_framework import generics
from .models import Comment
from .serializers import CommentSerializer

class CommentListCreateView(generics.ListCreateAPIView):
    queryset = Comment.objects.all()
    serializer_class = CommentSerializer
```

`api/urls.py`:

```python
from django.urls import path
from .views import CommentListCreateView

urlpatterns = [
    path('comments/', CommentListCreateView.as_view(), name='comment-list'),
]
```

`throttling_project/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),
]
```

---

## 4. ⚙️ Throttling sozlamalari

`settings.py` fayliga quyidagilarni qo‘shing:

```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '3/minute',
        'user': '10/minute',
    }
}
```

---

## 5. 🚀 Test qilish

`http://127.0.0.1:8000/api/comments/` manziliga:

- `POST` so‘rov yuboring (anonim va login qilingan user bilan).
- 3 martadan ortiq yuborsangiz, quyidagi xato qaytadi:

```json
{
  "detail": "Request was throttled. Expected available in 60 seconds."
}
```

---

## 6. 🛠 Custom Throttle Yaratish (ixtiyoriy)

Agar siz maxsus throttle logika yozmoqchi bo‘lsangiz:

`api/throttles.py`:

```python
from rest_framework.throttling import SimpleRateThrottle

class CustomIPThrottle(SimpleRateThrottle):
    scope = 'custom_ip'

    def get_cache_key(self, request, view):
        return self.get_ident(request)
```

`settings.py`ga qo‘shing:

```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'api.throttles.CustomIPThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'custom_ip': '2/minute',
    }
}
```

---

## ✅ Yakuniy Eslatma

- **Throttling** foydalanuvchilarni haddan tashqari ko‘p so‘rov yuborishdan saqlashda juda foydali.
- DRF bu funksiyani oson va kuchli tarzda taqdim etadi.
- Har bir throttle turining `scope` qiymati `settings.py`da belgilanishi kerak.

---

## 🎯 Vazifa

1. `Comment` modelini kengaytiring (masalan, `post_id`, `email` qo‘shing).
2. Har bir `user` faqat 5 ta comment yubora oladigan custom throttle yarating.
3. 429 xatoliklar uchun foydalanuvchi uchun qulay JSON xabar yozing.

---
