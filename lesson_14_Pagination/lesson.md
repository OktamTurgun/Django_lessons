# Lesson 14 — Pagination in Django REST Framework

## 1. Pagination nima va nima uchun kerak?

Pagination — katta hajmdagi ma'lumotlarni kichik bo'laklarga ajratib, foydalanuvchiga sahifa-sahifa ko'rsatish usuli.

Masalan:

- 10,000 ta mahsulot bor.
- Foydalanuvchi faqat 10 tasini bir sahifada ko'rishi kerak.
- Qolganlarini `page=2`, `page=3` kabi ko'rsatish mumkin.

**Foydasi:**

- **Tezkor ishlash** — Server bir paytda juda ko'p ma'lumot yubormaydi.
- **Front-end yukini kamaytirish** — Bir sahifada juda ko'p data bo'lmaydi.
- **Yuqori UX** — Foydalanuvchi aniq ko'rishni xohlagan bo'lagini oladi.

## 2. DRF'da pagination turlari

DRF'da asosiy pagination turlari:

1. **PageNumberPagination**  
   Oddiy raqamli sahifalash (`?page=2`). Ko'p ishlatiladi.

2. **LimitOffsetPagination**  
   Limit va offset orqali (`?limit=10&offset=20`). Katta datasetlarda yaxshi.

3. **CursorPagination**  
   Eng samarali va xavfsiz. Oldingi va keyingi sahifa uchun maxfiy token (`cursor`) ishlatiladi. Ma'lumotlar tartibi o'zgarmasligi shart.

## 3. Oddiy misol — PageNumberPagination

### a) settings.py:

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 5
}
```

> _Izoh:_ Har bir sahifada 5 ta obyekt bo'ladi.  
> URL misoli: `/api/products/?page=2`

### b) views.py:

```python
from rest_framework import generics
from .models import Product
from .serializers import ProductSerializer

class ProductListAPIView(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

> _Izoh:_ Pagination avtomatik qo'llanadi. DRF o'zi `count`, `next`, `previous`, `results` maydonlarini qo'shadi.

## 4. LimitOffsetPagination

### a) settings.py:

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 5
}
```

**URL misollari:**

- `/api/products/?limit=5` → 5 ta obyekt
- `/api/products/?limit=5&offset=10` → 11–15-chi obyektlar

**Afzalligi:** foydalanuvchi istagan joydan olish imkoniyati.

## 5. CursorPagination

### a) settings.py:

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.CursorPagination',
    'PAGE_SIZE': 5,
    'CURSOR_PAGINATION_ORDERING': 'created_at'
}
```

**Afzalligi:**  
Ma'lumotlar tartibi o'zgarmasa, eng xavfsiz. Foydalanuvchi keyingi sahifani token orqali oladi (`?cursor=abc123`).

## 6. Custom Pagination Class

```python
# pagination.py
from rest_framework.pagination import PageNumberPagination

class CustomPageNumberPagination(PageNumberPagination):
    page_size = 3
    page_size_query_param = 'page_size'
    max_page_size = 100
```

> **Izoh:**
>
> - **page_size_query_param** — foydalanuvchi o'zi sahifa hajmini belgilashi mumkin (`?page_size=10`).
> - **max_page_size** — haddan ortiq yukni oldini oladi.

### views.py:

```python
from rest_framework import generics
from .models import Product
from .serializers import ProductSerializer
from .pagination import CustomPageNumberPagination

class ProductListAPIView(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    pagination_class = CustomPageNumberPagination
```

## 7. Filtering + Pagination birgalikda

```python
class ProductListAPIView(generics.ListAPIView):
    serializer_class = ProductSerializer
    pagination_class = CustomPageNumberPagination

    def get_queryset(self):
        queryset = Product.objects.all()
        category = self.request.query_params.get('category')
        if category:
            queryset = queryset.filter(category__name=category)
        return queryset
```

> _Izoh:_ Filtering va pagination parallel ishlaydi — birinchi filter, keyin pagination.

## 8. Dars yakunidagi maslahatlar:

- **Katta dataset bilan ishlaganda** — always paginate, foydalanuvchiga bir yo‘la hamma ma’lumotni yubormang.
- **API foydalanuvchiga** `count`, `next`, `previous` maydonlari qulay navigatsiya beradi.
- **Foydalanuvchiga** `page_size` tanlash imkonini berish yaxshi UX beradi, lekin `max_page_size` bilan cheklang.
- **Filtering bilan birga ishlatish** — eng yaxshi kombinatsiya.
- **Production**'da ko‘p hollarda PageNumberPagination ishlatiladi, lekin CursorPagination real-time datada samaraliroq.
