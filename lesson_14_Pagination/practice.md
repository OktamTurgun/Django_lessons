# Practice — Pagination in Django REST Framework

Bu amaliyotda biz pagination’ni bosqichma-bosqich qo‘llaymiz:

1. Oddiy **PageNumberPagination**
2. **LimitOffsetPagination**
3. **Custom PageNumberPagination**
4. **Filtering + Pagination**
5. Qo‘shimcha challenge — **Custom response format**

---

## 1-Topshiriq — Oddiy sahifalash (PageNumberPagination)

**Vazifa:**

- `Book` modelini yarating (`title`, `author`, `published_date`).
- `ListAPIView` bilan paginate qiling.
- Har sahifada **4 ta obyekt** chiqsin.

### a) model — `models.py`

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    published_date = models.DateField()

    def __str__(self):
        return self.title
```

> _Izoh:_ Oddiy kitob modeli — `title`, `author` va `published_date`.

### b) serializer — `serializers.py`

```python
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'
```

### c) settings — `settings.py`

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 4
}
```

### d) view — `views.py`

```python
from rest_framework import generics
from .models import Book
from .serializers import BookSerializer

class BookListAPIView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

### e) urls — `urls.py`

```python
from django.urls import path
from .views import BookListAPIView

urlpatterns = [
    path('books/', BookListAPIView.as_view(), name='book-list'),
]
```

**Test qilish:**

```http
GET /api/books/
GET /api/books/?page=2
```

**Natija misoli:**

```json
{
  "count": 10,
  "next": "http://localhost:8000/api/books/?page=3",
  "previous": "http://localhost:8000/api/books/?page=1",
  "results": [
    {
      "id": 5,
      "title": "Book 5",
      "author": "Author 5",
      "published_date": "2020-05-01"
    }
  ]
}
```

---

## 2-Topshiriq — LimitOffsetPagination

**Vazifa:**

- `Movie` modelini yarating (`title`, `genre`, `rating`).
- Limit/Offset orqali paginate qiling.

### a) settings — `settings.py`

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 3
}
```

### b) model — `models.py`

```python
class Movie(models.Model):
    title = models.CharField(max_length=200)
    genre = models.CharField(max_length=50)
    rating = models.DecimalField(max_digits=3, decimal_places=1)

    def __str__(self):
        return self.title
```

### c) serializer — `serializers.py`

```python
class MovieSerializer(serializers.ModelSerializer):
    class Meta:
        model = Movie
        fields = '__all__'
```

### d) view — `views.py`

```python
class MovieListAPIView(generics.ListAPIView):
    queryset = Movie.objects.all()
    serializer_class = MovieSerializer
```

### e) urls — `urls.py`

```python
path('movies/', MovieListAPIView.as_view(), name='movie-list'),
```

**Test qilish:**

```http
GET /api/movies/?limit=3
GET /api/movies/?limit=3&offset=6
```

---

## 3-Topshiriq — Custom PageNumberPagination

**Vazifa:**

- `Product` modelini yarating (`name`, `price`, `stock`).
- `page_size` ni foydalanuvchi so‘rashi mumkin (`?page_size=10`).
- Eng katta `page_size` = **50** bo‘lsin.

### a) pagination — `pagination.py`

```python
from rest_framework.pagination import PageNumberPagination

class CustomPageNumberPagination(PageNumberPagination):
    page_size = 5
    page_size_query_param = 'page_size'
    max_page_size = 50
```

### b) model — `models.py`

```python
class Product(models.Model):
    name = models.CharField(max_length=200)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.PositiveIntegerField()

    def __str__(self):
        return self.name
```

### c) serializer — `serializers.py`

```python
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = '__all__'
```

### d) view — `views.py`

```python
from .pagination import CustomPageNumberPagination

class ProductListAPIView(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    pagination_class = CustomPageNumberPagination
```

### e) urls — `urls.py`

```python
path('products/', ProductListAPIView.as_view(), name='product-list'),
```

**Test qilish:**

```bash
GET /api/products/?page=2&page_size=10
```

---

## 4-Topshiriq — Filtering + Pagination

**Vazifa:**

- `Article` modelini yarating (`title`, `category`, `created_at`).
- `category` bo‘yicha filter + paginate qiling.

### a) model — `models.py`

```python
class Article(models.Model):
    title = models.CharField(max_length=200)
    category = models.CharField(max_length=50)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

### b) serializer — `serializers.py`

```python
class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = '__all__'
```

### c) view — `views.py`

```python
class ArticleListAPIView(generics.ListAPIView):
    serializer_class = ArticleSerializer
    pagination_class = CustomPageNumberPagination

    def get_queryset(self):
        queryset = Article.objects.all()
        category = self.request.query_params.get('category')
        if category:
            queryset = queryset.filter(category__iexact=category)
        return queryset
```

### d) urls — `urls.py`

```python
path('articles/', ArticleListAPIView.as_view(), name='article-list'),
```

**Test qilish:**

```http
GET /api/articles/?category=Tech&page=2&page_size=5
```

---

## 5-Qo‘shimcha Challenge — Custom Response Format

**Vazifa:** DRF pagination class’ini override qilib, natijani quyidagicha formatda qaytaring:

```json
{
    "total_items": 100,
    "total_pages": 20,
    "current_page": 2,
    "items": [...]
}
```

### a) pagination — `pagination.py`

```python
from rest_framework.pagination import PageNumberPagination
from rest_framework.response import Response
import math

class CustomResponsePagination(PageNumberPagination):
    page_size = 5

    def get_paginated_response(self, data):
        total_pages = math.ceil(self.page.paginator.count / self.page_size)
        return Response({
            "total_items": self.page.paginator.count,
            "total_pages": total_pages,
            "current_page": self.page.number,
            "items": data
        })
```

### b) view — `views.py`

```python
class CustomArticleListAPIView(generics.ListAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    pagination_class = CustomResponsePagination
```

### c) urls — `urls.py`

```python
path('articles/custom/', CustomArticleListAPIView.as_view(), name='custom-article-list'),
```

**Test qilish:**

```http
GET /api/articles/custom/?page=2
```

---

## 💡 Tavsiyalar:

- API’da **har doim pagination** ishlating, ayniqsa katta datasetlarda.
- `max_page_size` qo‘ying — foydalanuvchi serverni ortiqcha yuklamasligi uchun.
- Filtering + Pagination kombinatsiyasi ko‘p real loyihalarda ishlatiladi.
- Real-time yoki o‘zgarmas tartibdagi datada **CursorPagination** foydali.
