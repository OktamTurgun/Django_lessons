# 🟦 Dars 01: Django’ga Kirish

---

## 📌 Maqsad

Ushbu darsdan so‘ng siz quyidagilarni bilib olasiz:

- Django nima ekanligini va u qanday ishlashini
- Django arxitekturasi qanday asosga qurilganini (MTV)
- Django’ning asosiy imkoniyatlari va afzalliklari
- Django orqali web ilova qanday yaratilishini umumiy ko‘rinishda
- Django bilan yaratilgan mashhur saytlar haqida

---

## 🧠 Django nima?

**Django** — bu **Python dasturlash tilida yozilgan** kuchli web framework bo‘lib, u orqali **tez, xavfsiz va kengaytiriladigan web ilovalar** yaratish mumkin.

---

## 🏗 Django’ning Asosiy Fazilatlari

| Xususiyat          | Tavsif                                                                 |
|--------------------|------------------------------------------------------------------------|
| 🔧 **MTV Arxitekturasi**  | Model - Template - View (web ilovani tuzish arxitekturasi)                |
| 🚀 **Tez Prototiplash**   | Juda tez ishlaydigan web loyihalarni qisqa vaqtda yaratish mumkin         |
| 🔐 **Xavfsizlik**         | CSRF, SQL Injection kabi hujumlardan avtomatik himoya qiladi              |
| 🧱 **ORM**                | SQL yozmasdan ma’lumotlar bazasi bilan ishlash imkonini beradi           |
| ⚙️ **Admin Panel**        | Tayyor CRUD imkoniyatiga ega boshqaruv interfeysi                         |
| 🌐 **REST API Imkoniyati**| Django REST Framework orqali API yaratish oson va standartga mos         |

---

## 📂 Django Loyiha Tuzilishi

Django loyihasi **MTV arxitekturasi** asosida quriladi:

| Komponent | Vazifasi                                                                 |
|-----------|--------------------------------------------------------------------------|
| **Model** | Ma’lumotlar bazasi strukturasini aniqlaydi (klasslar orqali)            |
| **Template** | HTML sahifalar (foydalanuvchiga ko‘rinadigan qism)                  |
| **View** | Ma’lumotni olib, uni template’ga yuboradigan logika                      |

---

## 🔄 Django Ishlash Tartibi (Oddiy Misol)

1. 👤 Foydalanuvchi saytingizga kiradi (URL orqali)
2. 🔗 Django URL’ni `urls.py` orqali mos `View` ga yo‘naltiradi
3. 📦 `View` kerakli ma’lumotni `Model`dan oladi
4. 🖥 Ma’lumot `Template` orqali foydalanuvchiga ko‘rsatiladi

---

## 🌐 Django Bilan Yaratilgan Mashhur Saytlar

- **Instagram** — ijtimoiy tarmoq
- **Pinterest** — vizual izlovchi platforma
- **Mozilla** — brauzer ishlab chiquvchilari
- **Disqus** — komment tizimi
- **The Washington Post** — onlayn yangiliklar nashri

---

## 🧭 Keyingi Darslarda Nimalarni O‘rganamiz?

✅ Django framework’ini o‘rnatamiz  
✅ Yangi Django loyihasini yaratamiz  
✅ Django serverini ishga tushirib, brauzerda ko‘ramiz  

---

## 🔗 Qo‘shimcha Resurslar

- 🌍 Django rasmiy sayti: [https://www.djangoproject.com](https://www.djangoproject.com/)
- 📘 Django hujjatlari (EN): [https://docs.djangoproject.com/en/stable/](https://docs.djangoproject.com/en/stable/)

---

🚀 *Tayyormisiz? Endi amaliyotga o‘tamiz!* `practice.md` faylida siz Django o‘rnatish va dastlabki loyiha yaratishni bosqichma-bosqich o‘rganasiz.
