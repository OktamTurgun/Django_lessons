# Lesson 15 — Query Optimization in Django ORM: `select_related` va `prefetch_related`

---

## 1. Kirish: ORM va N+1 muammo

Django ORM juda qulay — model obyektlari orqali ma’lumotni SQL yozmasdan olishimiz mumkin.  
Ammo noto‘g‘ri yozilgan querysetlar **N+1 query problem** ga olib kelishi mumkin.

### N+1 muammo nima?
- Siz bitta asosiy so‘rov qilasiz (N=1).
- Keyin shu natijadagi har bir element uchun qo‘shimcha so‘rovlar bo‘ladi (N ta).
- Natijada **N+1** ta so‘rov bajariladi — bu esa performance’ni yomonlashtiradi.

**Oddiy misol:**
```python
books = Book.objects.all()  # 1 so‘rov
for book in books:
    print(book.author.name)  # Har bir kitob uchun author olish -> N so‘rov
```

---

## 2. `select_related` — ForeignKey va OneToOne optimizatsiyasi

**Qachon ishlatiladi?**  
ForeignKey yoki OneToOneField bilan bog‘langan modeldan ma’lumot olish kerak bo‘lsa.  

SQL’da `JOIN` ishlatadi va ma’lumotni bitta so‘rovda olib keladi.

### Misol:
**Model:**
```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```

**Noto‘g‘ri (N+1 muammo):**
```python
books = Book.objects.all()  # SELECT * FROM book;
for book in books:
    print(book.author.name)  # Har bir author uchun alohida SELECT
```

**SQL:**
```sql
SELECT * FROM book;
SELECT * FROM author WHERE id = 1;
SELECT * FROM author WHERE id = 2;
...
```

**To‘g‘ri (`select_related` bilan):**
```python
books = Book.objects.select_related('author')
for book in books:
    print(book.author.name)
```

**SQL:**
```sql
SELECT book.*, author.* 
FROM book
INNER JOIN author ON book.author_id = author.id;
```
✅ **Natija:** Bitta JOIN so‘rov bilan barcha ma’lumotlar olinadi.

---

## 3. `prefetch_related` — ManyToMany va Reverse ForeignKey optimizatsiyasi

**Qachon ishlatiladi?**  
`ManyToManyField` yoki reverse ForeignKey (ya’ni `related_name` orqali olish) bilan bog‘langan obyektlarni optimallashtirish.

Ikki alohida so‘rov bajaradi, lekin Python’da natijalarni cache’ga yig‘adi.

### Misol:
**Model:**
```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=200)
    authors = models.ManyToManyField(Author)
```

**Noto‘g‘ri (N+1 muammo):**
```python
books = Book.objects.all()
for book in books:
    for author in book.authors.all():
        print(author.name)
```

**SQL:**
```sql
SELECT * FROM book;
SELECT author.* FROM author
JOIN book_authors ON ...
-- Har bir kitob uchun alohida SELECT
```

**To‘g‘ri (`prefetch_related` bilan):**
```python
books = Book.objects.prefetch_related('authors')
for book in books:
    for author in book.authors.all():
        print(author.name)
```

**SQL:**
```sql
SELECT * FROM book;
SELECT author.*, book_authors.* 
FROM author
JOIN book_authors ON author.id = book_authors.author_id;
```
✅ **Natija:** Django ikkinchi so‘rov natijasini cache’laydi va Python ichida bog‘laydi.

---

## 4. `select_related` + `prefetch_related` birgalikda

Ba’zida ikkisini ham ishlatish kerak bo‘ladi.

**Misol:**
```python
books = Book.objects.select_related('publisher').prefetch_related('authors')
```
- `publisher` — ForeignKey (JOIN bilan keladi)  
- `authors` — ManyToMany (alohida SELECT bilan keladi)

---

## 5. Qachon ishlatmaslik kerak?

- Keraksiz `JOIN` qilmang: `select_related` keraksiz maydonlarni ham olib keladi.
- Katta `ManyToMany` datasetlarda `prefetch_related` xotiraga juda ko‘p yuk bo‘lishi mumkin.
- Faqat kerak bo‘lganda optimallashtiring.

---

## 6. Best Practices

- Har doim **Django Debug Toolbar** bilan so‘rov sonini tekshiring.
- Agar model ForeignKey orqali bog‘langan bo‘lsa → `select_related`
- Agar model ManyToMany yoki reverse relation bo‘lsa → `prefetch_related`
- Keraksiz maydonlarni olishdan saqlaning (`.only()`, `.defer()` ham yordam beradi).
- `select_related` — **JOIN**, `prefetch_related` — **alohida so‘rov + cache**.

---

## 7. Yakuniy tavsiyalar

- Avval kodni oddiy yozing, keyin profiling qiling.
- So‘rovlar sonini iloji boricha kamaytiring.
- ORM optimizatsiyasi backend performance’ini sezilarli oshiradi.
- Har doim real data bilan test qiling — kichik datasetlarda farq bilinmasligi mumkin.

**Keyingi dars:** Annotate va Aggregate