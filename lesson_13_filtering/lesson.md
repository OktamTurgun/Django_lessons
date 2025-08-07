# 📘 Lesson 13 — Filtering, Searching, Ordering in DRF

Ushbu darsda biz Django REST Framework’da API ma’lumotlarini **filtrlash**, **qidirish** va **tartiblash** metodlarini o‘rganamiz. Bu imkoniyatlar foydalanuvchiga kerakli ma’lumotni aniq va tez topish uchun xizmat qiladi.

---

## 1️⃣ Filtering (Filtrlash)

### 📦 `django-filter` kutubxonasi

DRF'da filtering uchun `django-filter` kutubxonasi ishlatiladi. Uni o‘rnatamiz:

```bash
pip install django-filter
```

`settings.py` faylida:

```python
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend'],
}
```

### ✅ Filterlash usullari

#### 1. `filterset_fields` orqali

```python
from rest_framework import viewsets
from .models import Product
from .serializers import ProductSerializer

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    filterset_fields = ['category', 'status']
```

#### 2. `FilterSet` klass yaratish

```python
import django_filters
from .models import Product

class ProductFilter(django_filters.FilterSet):
    class Meta:
        model = Product
        fields = {
            'price': ['lt', 'gt'],
            'created_at': ['date__gte', 'date__lte'],
        }
```

```python
class ProductViewSet(viewsets.ModelViewSet):
    ...
    filterset_class = ProductFilter
```

---

## 2️⃣ Searching (Qidirish)

### 🔍 `SearchFilter` dan foydalanish

```python
from rest_framework.filters import SearchFilter

class ProductViewSet(viewsets.ModelViewSet):
    ...
    filter_backends = [SearchFilter]
    search_fields = ['name', 'description']
```

Foydalanish:

```http
GET /products/?search=phone
```

---

## 3️⃣ Ordering (Tartiblash)

### 🔃 `OrderingFilter` bilan ishlash

```python
from rest_framework.filters import OrderingFilter

class ProductViewSet(viewsets.ModelViewSet):
    ...
    filter_backends = [OrderingFilter]
    ordering_fields = ['price', 'created_at']
    ordering = ['-created_at']
```

Foydalanish:

```http
GET /products/?ordering=price
GET /products/?ordering=-created_at
```

---

## 4️⃣ 🛠 Amaliy loyiha: Product API

Model:

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
    price = models.DecimalField(max_digits=8, decimal_places=2)
    category = models.CharField(max_length=50)
    status = models.CharField(max_length=20)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
```

Serializer:

```python
from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = '__all__'
```

ViewSet:

```python
from rest_framework import viewsets
from rest_framework.filters import SearchFilter, OrderingFilter
from django_filters.rest_framework import DjangoFilterBackend
from .models import Product
from .serializers import ProductSerializer

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    filter_backends = [DjangoFilterBackend, SearchFilter, OrderingFilter]
    filterset_fields = ['category', 'status']
    search_fields = ['name']
    ordering_fields = ['price', 'created_at']
    ordering = ['-created_at']
```

Router:

```python
from rest_framework.routers import DefaultRouter
from .views import ProductViewSet

router = DefaultRouter()
router.register('products', ProductViewSet, basename='product')
```

---

## 🧠 Yakuniy xulosa

| Tushuncha | Taqdimoti                                              |
| --------- | ------------------------------------------------------ |
| Filtering | Ma’lumotlarni kategoriya yoki holat bo‘yicha ajratish  |
| Searching | Matnli qidiruv (search) bilan kerakli elementni topish |
| Ordering  | Narx yoki sana bo‘yicha tartiblash (ordering)          |

---

## 📚 Qo‘shimcha manbalar

- [django-filter docs](https://django-filter.readthedocs.io/)
- [DRF filtering docs](https://www.django-rest-framework.org/api-guide/filtering/)

---

**Keyingi dars:** Pagination in DRF
