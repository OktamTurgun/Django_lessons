# 🧪 Amaliyot: Django REST API — Boshlang‘ich Amaliyotlar

---

## 📁 Loyihani tayyorlash

### 1. Django loyihasini yaratish

```bash
django-admin startproject restapiproject
cd restapiproject
```

### 2. `api` deb nomlangan app yarating:

```bash
python manage.py startapp api
```

### 3. Appni `settings.py` ga qo‘shing:

```python
# restapiproject/settings.py

INSTALLED_APPS = [
    ...
    'rest_framework',  # DRF kutubxonasi
    'api',             # Yaratilgan app
]
```

### 4. DRF kutubxonasini o‘rnatish:

```bash
pip install djangorestframework
```

DRF — Django REST Framework. Bu kutubxona RESTful API yaratish uchun eng mashhur va kuchli vositalardan biridir.

---

## 📄 Model yaratish

```python
# api/models.py
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

### Tushuntirish:

* `title` — post sarlavhasi.
* `content` — post matni.
* `created_at` — post yaratilgan vaqti (avtomatik).

---

## 🛠️ Migratsiya qilish

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 🧩 Serializer yozish

```python
# api/serializers.py
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__'
```

### Tushuntirish:

* `ModelSerializer` yordamida modelni avtomatik tarzda serializerga aylantiramiz.
* `fields = '__all__'` — modeldagi barcha maydonlarni ishlatamiz.

---

## 🌐 View (APIView) yozish

```python
# api/views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Post
from .serializers import PostSerializer

class PostListCreateAPIView(APIView):
    def get(self, request):
        posts = Post.objects.all()
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = PostSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

### Tushuntirish:

* `APIView` orqali GET va POST so‘rovlar uchun funksiya yozamiz.
* `get()` — barcha postlarni olib keladi.
* `post()` — yangi post qo‘shadi.

---

## 🧭 URL sozlash

```python
# api/urls.py
from django.urls import path
from .views import PostListCreateAPIView

urlpatterns = [
    path('posts/', PostListCreateAPIView.as_view(), name='post-list-create'),
]
```

```python
# restapiproject/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),
]
```

---

## 🚀 Test qilish

Serverni ishga tushiring:

```bash
python manage.py runserver
```

Brauzerda quyidagi URLga kiring:

```
http://127.0.0.1:8000/api/posts/
```

Agar hammasi to‘g‘ri ishlayotgan bo‘lsa:

* GET — mavjud postlar ro‘yxatini ko‘rasiz.
* POST — yangi post qo‘shishingiz mumkin.

---

## 🧪 Misol: POST so‘rov

`http://127.0.0.1:8000/api/posts/` manziliga quyidagicha JSON yuboring:

```json
{
    "title": "Birinchi post",
    "content": "Bu mening birinchi postim"
}
```

Natija quyidagicha bo‘ladi:

```json
{
    "id": 1,
    "title": "Birinchi post",
    "content": "Bu mening birinchi postim",
    "created_at": "2025-07-29T05:30:15.473Z"
}
```

---

## 💡 Foydali maslahatlar:

* Postman yoki Thunder Client yordamida test qilish oson.
* DRF’da boshqa view turlari ham mavjud: `GenericAPIView`, `ViewSet`, `ModelViewSet`.
* Har bir view uchun alohida serializer ishlatsa ham bo‘ladi.

---

**Keyingi dars:**
Test va deployment
