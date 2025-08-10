# Practice — `select_related` va `prefetch_related` bilan Query optimizatsiyasi

**Maqsad:** Django ORM’ida `select_related` va `prefetch_related` yordamida SQL so‘rovlar sonini kamaytirish, **N+1** muammosini hal qilish va samaradorlikni oshirish.

---

## 1-Topshiriq — `select_related` bilan optimizatsiya

### **Model:**

```python
# blog/models.py
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Post(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

    def __str__(self):
        return self.title
```

### **Serializer:**

```python
# blog/serializers.py
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    author_name = serializers.CharField(source='author.name')

    class Meta:
        model = Post
        fields = ['id', 'title', 'author_name']
```

### **View (optimallashtirishdan oldin):**

```python
# blog/views.py
from rest_framework.generics import ListAPIView
from .models import Post
from .serializers import PostSerializer

class PostListView(ListAPIView):
    queryset = Post.objects.all()  # ❌ N+1 muammo
    serializer_class = PostSerializer
```

### **View (optimallashtirishdan keyin):**

```python
class PostListView(ListAPIView):
    queryset = Post.objects.select_related('author')  # ✅ Optimallashtirilgan
    serializer_class = PostSerializer
```

### **URL:**

```python
# blog/urls.py
from django.urls import path
from .views import PostListView

urlpatterns = [
    path('posts/', PostListView.as_view(), name='post-list'),
]
```

### **Test query:**

```python
python manage.py shell_plus --print-sql

from blog.models import Post

# ❌ 1 + N query
for post in Post.objects.all():
    print(post.author.name)

# ✅ 1 query
for post in Post.objects.select_related('author'):
    print(post.author.name)
```

**So‘rovlar soni:**

- **Oldin:** 1 + N (masalan, 1 + 10 = 11 query)
- **Keyin:** 1 query

**Izoh:**  
`select_related` — ForeignKey yoki OneToOne maydonlar uchun ishlaydi va **JOIN** orqali bitta queryda ma’lumotni olib keladi.

---

## 2-Topshiriq — `prefetch_related` bilan optimizatsiya

### **Model:**

```python
# blog/models.py
class Tag(models.Model):
    name = models.CharField(max_length=50)

class Article(models.Model):
    title = models.CharField(max_length=200)
    tags = models.ManyToManyField(Tag)
```

### **Serializer:**

```python
# blog/serializers.py
from .models import Article, Tag
from rest_framework import serializers

class TagSerializer(serializers.ModelSerializer):
    class Meta:
        model = Tag
        fields = ['id', 'name']

class ArticleSerializer(serializers.ModelSerializer):
    tags = TagSerializer(many=True)

    class Meta:
        model = Article
        fields = ['id', 'title', 'tags']
```

### **View (optimallashtirishdan oldin):**

```python
class ArticleListView(ListAPIView):
    queryset = Article.objects.all()  # ❌ N+1 muammo
    serializer_class = ArticleSerializer
```

### **View (optimallashtirishdan keyin):**

```python
class ArticleListView(ListAPIView):
    queryset = Article.objects.prefetch_related('tags')  # ✅
    serializer_class = ArticleSerializer
```

### **Test query:**

```python
from blog.models import Article

# ❌ Har safar alohida query
for article in Article.objects.all():
    for tag in article.tags.all():
        print(tag.name)

# ✅ 2 query
for article in Article.objects.prefetch_related('tags'):
    for tag in article.tags.all():
        print(tag.name)
```

**So‘rovlar soni:**

- **Oldin:** 1 + N
- **Keyin:** 2 query (asosiy model + bog‘langan jadval)

**Izoh:**  
`prefetch_related` — ManyToMany yoki **reverse ForeignKey** bog‘lanishlar uchun ishlaydi.

---

## 3-Topshiriq — `select_related` + `prefetch_related` kombinatsiyasi

### **Model:**

```python
class Category(models.Model):
    name = models.CharField(max_length=100)

class Product(models.Model):
    title = models.CharField(max_length=200)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
    tags = models.ManyToManyField(Tag)
```

### **View (kombinatsiya):**

```python
class ProductListView(ListAPIView):
    queryset = Product.objects.select_related('category').prefetch_related('tags')
    serializer_class = ProductSerializer
```

**Izoh:**

- `select_related('category')` — ForeignKey uchun
- `prefetch_related('tags')` — ManyToMany uchun

**So‘rovlar soni:**

- **Kombinatsiyadan keyin:** 3 query (asosiy, ForeignKey, ManyToMany)

---

## 4-Topshiriq — Qachon ishlatmaslik kerak

**Misol:**

```python
# Juda katta hajmdagi many-to-many ma’lumotni ishlatmasangiz ham oldindan yuklab olish
products = Product.objects.prefetch_related('tags')  # ❌ Ehtiyoj bo‘lmasa, xotirani isrof qiladi
```

**Izoh:**  
Agar sizga bog‘langan jadval ma’lumoti kerak bo‘lmasa, `select_related` yoki `prefetch_related` qo‘llamang. Bu xotira va so‘rovlarni ortiqcha ishlatishga olib keladi.

---

## Xulosa

| Metod            | Qaysi bog‘lanishda ishlaydi              | Query soni kamayishi      | Qachon ishlatish kerak                     |
|------------------|------------------------------------------|---------------------------|---------------------------------------------|
| **select_related** | ForeignKey, OneToOne                    | JOIN orqali bitta         | Bog‘langan obyektlar kerak bo‘lsa           |
| **prefetch_related** | ManyToMany, reverse ForeignKey         | 2 yoki ko‘p                | Ko‘p obyektlar kerak bo‘lsa                 |
