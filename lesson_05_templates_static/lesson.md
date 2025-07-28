# 📝 5-dars: Django Templates va Static Files

---

## 1. Django Templates nima?
- **Template** — HTML fayllar bo'lib, dinamik ma'lumotlarni ko'rsatish uchun ishlatiladi.
- Django template language (DTL) yordamida Python ma'lumotlarini HTML ga o'tkazadi.
- Template'lar orqali kod va dizayn ajratiladi (separation of concerns).
- Batafsil: [Django Templates documentation](https://docs.djangoproject.com/en/stable/topics/templates/)

**Template tuzilishi:**
- **Variables** — `{{ variable }}`
- **Tags** — `{% tag %}`
- **Filters** — `{{ variable|filter }}`
- **Comments** — `{# comment #}`

---

## 2. Template tuzilishi

### Template papka strukturasi
```
mysite/
├── templates/
│   ├── base.html
│   ├── 404.html
│   └── 500.html
└── blog/
    └── templates/
        └── blog/
            ├── post_list.html
            ├── post_detail.html
            └── post_form.html
```

### Settings.py da template sozlash
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # Global templates
        'APP_DIRS': True,  # App templates
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

---

## 3. Template Variables

### Oddiy o'zgaruvchilar
```html
<h1>{{ post.title }}</h1>
<p>{{ post.content }}</p>
<small>{{ post.created_at|date:"d.m.Y" }}</small>
```

### Dictionary elementlari
```python
# views.py
context = {
    'user': {'name': 'John', 'age': 25},
    'posts': Post.objects.all()
}
```

```html
<!-- Template -->
<p>Foydalanuvchi: {{ user.name }}, yoshi: {{ user.age }}</p>
```

### List elementlari
```python
# views.py
context = {'numbers': [1, 2, 3, 4, 5]}
```

```html
<!-- Template -->
<p>Birinchi raqam: {{ numbers.0 }}</p>
<p>Oxirgi raqam: {{ numbers.4 }}</p>
```

---

## 4. Template Tags

### For loop
```html
{% for post in posts %}
    <article>
        <h2>{{ post.title }}</h2>
        <p>{{ post.content|truncatewords:30 }}</p>
    </article>
{% empty %}
    <p>Hozircha postlar yo'q.</p>
{% endfor %}
```

### If-else
```html
{% if user.is_authenticated %}
    <p>Xush kelibsiz, {{ user.username }}!</p>
    <a href="{% url 'blog:post_create' %}">Yangi post</a>
{% else %}
    <p>Iltimos, tizimga kiring.</p>
    <a href="{% url 'login' %}">Kirish</a>
{% endif %}
```

### URL tag
```html
<a href="{% url 'blog:post_detail' post.id %}">{{ post.title }}</a>
<a href="{% url 'blog:post_list' %}">Barcha postlar</a>
```

### Include tag
```html
{% include 'blog/includes/header.html' %}

<main>
    {% block content %}
    {% endblock %}
</main>

{% include 'blog/includes/footer.html' %}
```

### Extends tag
```html
{% extends 'blog/base.html' %}

{% block title %}Postlar{% endblock %}

{% block content %}
    <!-- Sahifa mazmuni -->
{% endblock %}
```

---

## 5. Template Filters

### Oddiy filter'lar
```html
<!-- Matn filter'lari -->
{{ post.title|upper }}
{{ post.content|lower|truncatewords:20 }}

<!-- Sana filter'lari -->
{{ post.created_at|date:"d.m.Y H:i" }}
{{ post.created_at|time:"H:i" }}

<!-- Raqam filter'lari -->
{{ post.views|default:"0" }}
{{ post.price|floatformat:2 }}

<!-- Xavfsizlik filter'lari -->
{{ user_input|escape }}
{{ html_content|safe }}
```

### Custom filter yaratish
**Fayl:** `blog/templatetags/blog_extras.py`
```python
from django import template
from django.utils.safestring import mark_safe

register = template.Library()

@register.filter
def highlight_search(text, search_term):
    """Qidiruv so'zini yorqin qilish"""
    if search_term in text:
        highlighted = text.replace(search_term, f'<mark>{search_term}</mark>')
        return mark_safe(highlighted)
    return text

@register.filter
def word_count(text):
    """So'zlar sonini hisoblash"""
    return len(text.split())
```

**Template'da ishlatish:**
```html
{% load blog_extras %}

<p>{{ post.content|highlight_search:query }}</p>
<p>So'zlar soni: {{ post.content|word_count }}</p>
```

---

## 6. Template Inheritance

### Base template
**Fayl:** `templates/base.html`
```html
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Blog{% endblock %}</title>
    {% load static %}
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
</head>
<body>
    <header>
        {% include 'includes/header.html' %}
    </header>
    
    <main>
        {% block content %}
        {% endblock %}
    </main>
    
    <footer>
        {% include 'includes/footer.html' %}
    </footer>
    
    <script src="{% static 'js/main.js' %}"></script>
</body>
</html>
```

### Child template
**Fayl:** `blog/templates/blog/post_list.html`
```html
{% extends 'base.html' %}

{% block title %}Barcha postlar{% endblock %}

{% block content %}
<h1>Blog postlar</h1>
{% for post in posts %}
    <article>
        <h2>{{ post.title }}</h2>
        <p>{{ post.content|truncatewords:30 }}</p>
    </article>
{% endfor %}
{% endblock %}
```

---

## 7. Static Files

### Static files nima?
- **Static files** — CSS, JavaScript, rasm va boshqa fayllar
- Har safar o'zgarmaydigan fayllar
- Brauzer tomonidan to'g'ridan-to'g'ri yuklanadi

### Static files sozlash
**Settings.py:**
```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    BASE_DIR / "static",
]
STATIC_ROOT = BASE_DIR / 'staticfiles'
```

### Static files strukturasi
```
mysite/
├── static/
│   ├── css/
│   │   ├── style.css
│   │   └── bootstrap.css
│   ├── js/
│   │   ├── main.js
│   │   └── jquery.js
│   ├── images/
│   │   ├── logo.png
│   │   └── banner.jpg
│   └── fonts/
│       └── custom-font.woff
```

### Template'da static files ishlatish
```html
{% load static %}

<!-- CSS -->
<link rel="stylesheet" href="{% static 'css/style.css' %}">

<!-- JavaScript -->
<script src="{% static 'js/main.js' %}"></script>

<!-- Rasm -->
<img src="{% static 'images/logo.png' %}" alt="Logo">

<!-- Favicon -->
<link rel="icon" href="{% static 'images/favicon.ico' %}">
```

---

## 8. CSS va JavaScript

### CSS fayl
**Fayl:** `static/css/style.css`
```css
/* Reset */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

/* Body */
body {
    font-family: 'Arial', sans-serif;
    line-height: 1.6;
    color: #333;
    background-color: #f4f4f4;
}

/* Header */
header {
    background: #333;
    color: white;
    padding: 1rem;
}

nav {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

nav a {
    color: white;
    text-decoration: none;
    padding: 0.5rem 1rem;
}

nav a:hover {
    background: #555;
}

/* Main content */
main {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
}

/* Posts */
.post {
    background: white;
    padding: 1rem;
    margin: 1rem 0;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.post h2 {
    color: #333;
    margin-bottom: 0.5rem;
}

.post p {
    color: #666;
    margin-bottom: 1rem;
}

/* Forms */
form {
    background: white;
    padding: 2rem;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

input, textarea, select {
    width: 100%;
    padding: 0.5rem;
    margin: 0.5rem 0;
    border: 1px solid #ddd;
    border-radius: 3px;
}

button {
    background: #333;
    color: white;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 3px;
    cursor: pointer;
}

button:hover {
    background: #555;
}

/* Responsive */
@media (max-width: 768px) {
    main {
        padding: 1rem;
    }
    
    nav {
        flex-direction: column;
    }
}
```

### JavaScript fayl
**Fayl:** `static/js/main.js`
```javascript
// DOM ready
document.addEventListener('DOMContentLoaded', function() {
    // Form validation
    const forms = document.querySelectorAll('form');
    forms.forEach(form => {
        form.addEventListener('submit', function(e) {
            const requiredFields = form.querySelectorAll('[required]');
            let isValid = true;
            
            requiredFields.forEach(field => {
                if (!field.value.trim()) {
                    isValid = false;
                    field.style.borderColor = 'red';
                } else {
                    field.style.borderColor = '#ddd';
                }
            });
            
            if (!isValid) {
                e.preventDefault();
                alert('Iltimos, barcha maydonlarni to\'ldiring!');
            }
        });
    });
    
    // Smooth scrolling
    const links = document.querySelectorAll('a[href^="#"]');
    links.forEach(link => {
        link.addEventListener('click', function(e) {
            e.preventDefault();
            const target = document.querySelector(this.getAttribute('href'));
            if (target) {
                target.scrollIntoView({ behavior: 'smooth' });
            }
        });
    });
    
    // Search functionality
    const searchInput = document.querySelector('#search-input');
    if (searchInput) {
        searchInput.addEventListener('input', function() {
            const query = this.value.toLowerCase();
            const posts = document.querySelectorAll('.post');
            
            posts.forEach(post => {
                const title = post.querySelector('h2').textContent.toLowerCase();
                const content = post.querySelector('p').textContent.toLowerCase();
                
                if (title.includes(query) || content.includes(query)) {
                    post.style.display = 'block';
                } else {
                    post.style.display = 'none';
                }
            });
        });
    }
});

// AJAX request example
function loadMorePosts() {
    fetch('/blog/api/posts/')
        .then(response => response.json())
        .then(data => {
            // Handle response
            console.log(data);
        })
        .catch(error => {
            console.error('Error:', error);
        });
}
```

---

## 9. Template Includes

### Header include
**Fayl:** `templates/includes/header.html`
```html
<nav>
    <div class="logo">
        <a href="{% url 'blog:post_list' %}">Blog</a>
    </div>
    
    <ul class="nav-links">
        <li><a href="{% url 'blog:post_list' %}">Postlar</a></li>
        <li><a href="{% url 'blog:about' %}">Haqida</a></li>
        <li><a href="{% url 'blog:contact' %}">Aloqa</a></li>
        
        {% if user.is_authenticated %}
            <li><a href="{% url 'blog:post_create' %}">Yangi post</a></li>
            <li><a href="{% url 'logout' %}">Chiqish</a></li>
        {% else %}
            <li><a href="{% url 'login' %}">Kirish</a></li>
        {% endif %}
    </ul>
</nav>
```

### Footer include
**Fayl:** `templates/includes/footer.html`
```html
<footer>
    <div class="footer-content">
        <div class="footer-section">
            <h3>Blog haqida</h3>
            <p>Bu blog Django framework yordamida yaratilgan.</p>
        </div>
        
        <div class="footer-section">
            <h3>Foydali havolalar</h3>
            <ul>
                <li><a href="{% url 'blog:post_list' %}">Postlar</a></li>
                <li><a href="{% url 'blog:about' %}">Haqida</a></li>
                <li><a href="{% url 'blog:contact' %}">Aloqa</a></li>
            </ul>
        </div>
        
        <div class="footer-section">
            <h3>Ijtimoiy tarmoqlar</h3>
            <div class="social-links">
                <a href="#" class="social-link">Facebook</a>
                <a href="#" class="social-link">Twitter</a>
                <a href="#" class="social-link">Instagram</a>
            </div>
        </div>
    </div>
    
    <div class="footer-bottom">
        <p>&copy; 2024 Blog. Barcha huquqlar himoyalangan.</p>
    </div>
</footer>
```

---

## 10. Template Context Processors

### Custom context processor
**Fayl:** `blog/context_processors.py`
```python
def global_context(request):
    """Global context barcha template'larga qo'shiladi"""
    return {
        'site_name': 'My Blog',
        'site_description': 'Django blog loyihasi',
        'categories': Category.objects.all(),
        'recent_posts': Post.objects.filter(is_published=True)[:5],
    }
```

### Settings.py ga qo'shish
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
                'blog.context_processors.global_context',  # Qo'shing
            ],
        },
    },
]
```

### Template'da ishlatish
```html
<!-- Har qanday template'da ishlatish mumkin -->
<h1>{{ site_name }}</h1>
<p>{{ site_description }}</p>

{% for category in categories %}
    <a href="{% url 'blog:category_posts' category.name %}">
        {{ category.name }}
    </a>
{% endfor %}
```

---

## 11. Template Optimization

### Template caching
```python
# views.py
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)  # 15 daqiqa cache
def post_list(request):
    posts = Post.objects.all()
    return render(request, 'blog/post_list.html', {'posts': posts})
```

### Template fragment caching
```html
{% load cache %}

{% cache 300 'recent_posts' %}
    <h3>So'nggi postlar</h3>
    {% for post in recent_posts %}
        <div>{{ post.title }}</div>
    {% endfor %}
{% endcache %}
```

### Template compression
```html
<!-- O'qish uchun -->
{% if user.is_authenticated %}
    <p>Xush kelibsiz!</p>
{% endif %}

<!-- Kompakt versiya -->
{% if user.is_authenticated %}<p>Xush kelibsiz!</p>{% endif %}
```

---

## 12. Amaliyot uchun tavsiyalar

### Template'lar uchun
1. **Base template** — har doim asosiy template yarating
2. **Include'lar** — kichik qismlarni alohida fayllarga ajrating
3. **Block'lar** — template inheritance uchun block'lar ishlating
4. **Filter'lar** — template filter'lardan foydalaning
5. **Security** — XSS hujumlaridan himoyalang

### Static files uchun
1. **Tashkil qilish** — fayllarni papkalarga ajrating
2. **Optimization** — rasm va fayllarni siqish
3. **CDN** — katta loyihalar uchun CDN ishlating
4. **Caching** — static files uchun cache sozlang

### Performance uchun
1. **Template caching** — tez-tez ishlatiladigan qismlarni cache qiling
2. **Database queries** — N+1 muammosini hal qiling
3. **Static files** — production'da collectstatic ishlating
4. **Minification** — CSS va JS fayllarni siqish

---

## 13. Foydali maslahatlar

- **Template'lar** — presentation uchun ishlating
- **Logic** — view'larda business logic yozing
- **Reusability** — template'lar qayta ishlatilishi mumkin bo'lsin
- **Maintainability** — kodni tushunarli va tartibda saqlang
- **Performance** — template'lar tez yuklanishi kerak
- **Security** — foydalanuvchi ma'lumotlarini tozalang

---

**Keyingi dars:**  
6-dars: Django Forms va User Management  
Bu darsda siz Django form'larini yaratish, foydalanuvchi ma'lumotlarini boshqarish va validatsiya ishlarini o'rganasiz. 