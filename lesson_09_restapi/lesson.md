# 📘 9-DARS: Django REST API — Boshlang‘ich Kirish

---

## 📌 Maqsad:

Django REST Framework (DRF) yordamida REST API yaratishning asosiy tushunchalari bilan tanishish: Serializer, APIView, va `@api_view` dekoratori.

---

## 📁 1. Django REST Framework nima?

**Django REST Framework (DRF)** — Django asosida RESTful API lar yaratish uchun qulay va kuchli kutubxona.

### 🔧 REST API degani nima?

* **REST** — "Representational State Transfer" degan atamaning qisqartmasi
* REST API — bu veb xizmatlar bilan aloqani JSON formatda amalga oshirish usuli
* HTTP metodlaridan foydalaniladi: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`

## 📁 2. DRF o‘rnatish va sozlash

```bash
pip install djangorestframework
```

**settings.py** fayliga quyidagilarni qo‘shamiz:

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

---

## 📁 3. Serializer nima?

Serializer model obyektlarini JSON formatiga va aksincha aylantirib beradi.

### 📦 Misol:

```python
# blog/serializers.py
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__'
```

---

## 📁 4. APIView bilan API yozish

```python
# blog/api_views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from .models import Post
from .serializers import PostSerializer

class PostListView(APIView):
    def get(self, request):
        posts = Post.objects.all()
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)
```

**urls.py**:

```python
# blog/urls.py
from django.urls import path
from .api_views import PostListView

urlpatterns = [
    path('api/posts/', PostListView.as_view(), name='post-list')
]
```

---

## 📁 5. `@api_view` dekoratori orqali API yozish

```python
# blog/api_views.py
tfrom rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Post
from .serializers import PostSerializer

@api_view(['GET'])
def get_posts(request):
    posts = Post.objects.all()
    serializer = PostSerializer(posts, many=True)
    return Response(serializer.data)
```

**urls.py**:

```python
from .api_views import get_posts

urlpatterns = [
    path('api/posts-alt/', get_posts, name='post-list-alt')
]
```

---

## 📁 6. Postman orqali test qilish

1. `python manage.py runserver` bilan serverni ishga tushiring
2. Postman oching, `GET http://127.0.0.1:8000/api/posts/` ni yuboring
3. JSON formatda natija ko‘rasiz

---

## ✅ Yakuniy xulosa:

| Tushuncha      | Taqdimoti                                     |
| -------------- | --------------------------------------------- |
| **Serializer** | Ma’lumotlarni JSON ga va aksincha aylantirish |
| **APIView**    | Class asosidagi API yozish                    |
| **@api\_view** | Funksiya asosida API yozish                   |
| **DRF**        | Django uchun RESTful API kutubxonasi          |

---

**Keyingi dars:**
10-dars: Test va deployment
