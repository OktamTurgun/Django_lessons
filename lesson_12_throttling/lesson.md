# 🧭 Lesson 12 — Throttling (So‘rovlarni cheklash)

> Django REST Framework (DRF)da **throttling** — bu foydalanuvchining muayyan vaqt ichida qancha so‘rov yuborishiga cheklov qo‘yish tizimidir. U spam, DDoS yoki resurslarni ortiqcha yuklashga qarshi himoya chorasi sifatida ishlatiladi.

---

## 📌 Throttling nima?

Throttling — bu foydalanuvchining APIga yuboradigan so‘rovlar soniga vaqt oralig‘ida cheklov qo‘yish.

🧠 Farqi:

- **Authentication**: Kim ekanligini aniqlaydi.
- **Permissions**: Siz nima qilish huquqiga egasiz?
- **Throttling**: Siz qancha tez-tez qilishingiz mumkin?

---

## ⏱ Throttle turlari (Built-in Classes)

Django REST Framework quyidagi tayyor throttling klasslarini taqdim etadi:

| Throttle turi        | Tavsifi                                                  |
| -------------------- | -------------------------------------------------------- |
| `AnonRateThrottle`   | Ro‘yxatdan o‘tmagan foydalanuvchilar uchun cheklov       |
| `UserRateThrottle`   | Auth bo‘lgan foydalanuvchilar uchun cheklov              |
| `ScopedRateThrottle` | Maqsadli (scope bilan) endpointlar uchun moslashtirilgan |

---

## ⚙️ 1. Throttle sozlamalarini faollashtirish

`settings.py` fayliga quyidagilarni qo‘shing:

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": [
        "rest_framework.throttling.AnonRateThrottle",
        "rest_framework.throttling.UserRateThrottle",
    ],
    "DEFAULT_THROTTLE_RATES": {
        "anon": "5/minute",  # har daqiqada 5 ta so‘rov
        "user": "20/minute",  # auth bo‘lgan foydalanuvchi uchun
    }
}
```

---

## 🔐 2. ScopedRateThrottle

Agar siz muayyan endpoint uchun o‘ziga xos cheklov qo‘ymoqchi bo‘lsangiz, `ScopedRateThrottle` dan foydalaning.

### a) settings.py

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": [
        "rest_framework.throttling.ScopedRateThrottle",
    ],
    "DEFAULT_THROTTLE_RATES": {
        "low_request": "3/minute",
        "high_request": "10/minute",
    }
}
```

### b) views.py

```python
from rest_framework.throttling import ScopedRateThrottle
from rest_framework.views import APIView
from rest_framework.response import Response

class MyLimitedView(APIView):
    throttle_scope = "low_request"  # shu scope bo‘yicha cheklanadi
    throttle_classes = [ScopedRateThrottle]

    def get(self, request):
        return Response({"message": "Siz muvaffaqiyatli so‘rov yubordingiz!"})
```

---

## 🚨 ThrottleExceeded xatosi

Agar foydalanuvchi cheklovdan oshib ketsa, u quyidagi xatoni oladi:

```json
{
  "detail": "Request was throttled. Expected available in 59 seconds."
}
```

---

## 🔄 Maxsus (custom) throttle klass yozish

```python
from rest_framework.throttling import SimpleRateThrottle

class CustomIPThrottle(SimpleRateThrottle):
    scope = 'custom_ip'

    def get_cache_key(self, request, view):
        return self.get_ident(request)  # IP manzilga asoslanadi
```

Va `settings.py`:

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": [
        "myapp.throttling.CustomIPThrottle",
    ],
    "DEFAULT_THROTTLE_RATES": {
        "custom_ip": "5/hour"
    }
}
```

---

## 🧪 Tavsiya qilingan amaliy mashqlar:

1. `AnonRateThrottle` va `UserRateThrottle`ni sozlab, Postman orqali test qiling.
2. `ScopedRateThrottle` yordamida ikki xil endpointga turli scope bering.
3. ThrottleExceeded xatosini olish uchun testlar yozing.
4. `CustomIPThrottle` yozib, IP manzil bo‘yicha cheklov kiriting.

---

## 📚 Qo‘shimcha manbalar

- [DRF Throttling rasmiy hujjat](https://www.django-rest-framework.org/api-guide/throttling/)
- DRF source code’da `throttling.py` ni o‘rganish
- Amaliy loyihalarda throttlingni performance balans uchun qo‘llash

---

✅ **Keyingi dars**:  
**Lesson 13 — Filtering, Searching va Ordering**
