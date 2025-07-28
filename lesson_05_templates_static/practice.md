# 🛠️ 5-dars: Django Templates va Static Files - Amaliyot

---

## 📋 Mashg'ulot 1: Template tuzilishi va base template

### Vazifa
Template papka strukturasi yarating va base template sozlang.

### Qadamlar

**1. Template papkalarini yarating:**
```bash
# Asosiy template papkasi
mkdir templates
mkdir templates/includes

# Blog app template papkasi
mkdir blog/templates
mkdir blog/templates/blog
```

**2. Base template yarating (`templates/base.html`):**
```html
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Blog{% endblock %}</title>
    {% load static %}
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
    <link rel="icon" href="{% static 'images/favicon.ico' %}">
</head>
<body>
    <header>
        {% include 'includes/header.html' %}
    </header>
    
    <main class="container">
        {% if messages %}
            <div class="messages">
                {% for message in messages %}
                    <div class="message message-{{ message.tags }}">
                        {{ message }}
                    </div>
                {% endfor %}
            </div>
        {% endif %}
        
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

**3. Header include yarating (`templates/includes/header.html`):**
```html
<nav class="navbar">
    <div class="nav-container">
        <div class="nav-logo">
            <a href="{% url 'blog:post_list' %}">Blog</a>
        </div>
        
        <ul class="nav-menu">
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
        
        <div class="hamburger">
            <span></span>
            <span></span>
            <span></span>
        </div>
    </div>
</nav>
```

**4. Footer include yarating (`templates/includes/footer.html`):**
```html
<footer class="footer">
    <div class="footer-content">
        <div class="footer-section">
            <h3>Blog haqida</h3>
            <p>Bu blog Django framework yordamida yaratilgan va zamonaviy web texnologiyalarini o'rgatadi.</p>
        </div>
        
        <div class="footer-section">
            <h3>Foydali havolalar</h3>
            <ul>
                <li><a href="{% url 'blog:post_list' %}">Barcha postlar</a></li>
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

### Natija
Template tuzilishi yaratildi va base template sozlandi.

---

## 📋 Mashg'ulot 2: Static files tuzilishi

### Vazifa
Static files papka strukturasi yarating va CSS/JS fayllarini qo'shing.

### Qadamlar

**1. Static files papkalarini yarating:**
```bash
mkdir static
mkdir static/css
mkdir static/js
mkdir static/images
mkdir static/fonts
```

**2. CSS fayl yarating (`static/css/style.css`):**
```css
/* Reset va asosiy stillar */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    line-height: 1.6;
    color: #333;
    background-color: #f8f9fa;
}

/* Container */
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
}

/* Header va Navigation */
.navbar {
    background: #2c3e50;
    padding: 1rem 0;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.nav-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.nav-logo a {
    color: white;
    text-decoration: none;
    font-size: 1.5rem;
    font-weight: bold;
}

.nav-menu {
    display: flex;
    list-style: none;
    gap: 2rem;
}

.nav-menu a {
    color: white;
    text-decoration: none;
    padding: 0.5rem 1rem;
    border-radius: 3px;
    transition: background-color 0.3s;
}

.nav-menu a:hover {
    background-color: #34495e;
}

/* Hamburger menu */
.hamburger {
    display: none;
    flex-direction: column;
    cursor: pointer;
}

.hamburger span {
    width: 25px;
    height: 3px;
    background: white;
    margin: 3px 0;
    transition: 0.3s;
}

/* Main content */
main {
    min-height: calc(100vh - 200px);
    padding: 2rem 0;
}

/* Posts */
.post {
    background: white;
    padding: 2rem;
    margin: 1rem 0;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    transition: transform 0.3s;
}

.post:hover {
    transform: translateY(-2px);
}

.post h2 {
    color: #2c3e50;
    margin-bottom: 1rem;
    font-size: 1.5rem;
}

.post h2 a {
    color: inherit;
    text-decoration: none;
}

.post h2 a:hover {
    color: #3498db;
}

.post p {
    color: #666;
    margin-bottom: 1rem;
    line-height: 1.8;
}

.post-meta {
    color: #999;
    font-size: 0.9rem;
}

/* Forms */
.form-container {
    background: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    max-width: 600px;
    margin: 0 auto;
}

.form-group {
    margin-bottom: 1.5rem;
}

.form-group label {
    display: block;
    margin-bottom: 0.5rem;
    font-weight: bold;
    color: #333;
}

.form-group input,
.form-group textarea,
.form-group select {
    width: 100%;
    padding: 0.75rem;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 1rem;
    transition: border-color 0.3s;
}

.form-group input:focus,
.form-group textarea:focus,
.form-group select:focus {
    outline: none;
    border-color: #3498db;
    box-shadow: 0 0 0 2px rgba(52, 152, 219, 0.2);
}

.btn {
    background: #3498db;
    color: white;
    padding: 0.75rem 1.5rem;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 1rem;
    transition: background-color 0.3s;
}

.btn:hover {
    background: #2980b9;
}

.btn-danger {
    background: #e74c3c;
}

.btn-danger:hover {
    background: #c0392b;
}

/* Messages */
.messages {
    margin-bottom: 2rem;
}

.message {
    padding: 1rem;
    margin: 0.5rem 0;
    border-radius: 4px;
    border-left: 4px solid;
}

.message-success {
    background: #d4edda;
    border-color: #28a745;
    color: #155724;
}

.message-error {
    background: #f8d7da;
    border-color: #dc3545;
    color: #721c24;
}

.message-warning {
    background: #fff3cd;
    border-color: #ffc107;
    color: #856404;
}

.message-info {
    background: #d1ecf1;
    border-color: #17a2b8;
    color: #0c5460;
}

/* Footer */
.footer {
    background: #2c3e50;
    color: white;
    padding: 2rem 0 1rem;
    margin-top: 3rem;
}

.footer-content {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 2rem;
}

.footer-section h3 {
    margin-bottom: 1rem;
    color: #3498db;
}

.footer-section ul {
    list-style: none;
}

.footer-section ul li {
    margin-bottom: 0.5rem;
}

.footer-section a {
    color: #bdc3c7;
    text-decoration: none;
    transition: color 0.3s;
}

.footer-section a:hover {
    color: white;
}

.social-links {
    display: flex;
    gap: 1rem;
}

.social-link {
    padding: 0.5rem 1rem;
    background: #34495e;
    border-radius: 3px;
    transition: background-color 0.3s;
}

.social-link:hover {
    background: #3498db;
}

.footer-bottom {
    text-align: center;
    margin-top: 2rem;
    padding-top: 1rem;
    border-top: 1px solid #34495e;
}

/* Responsive design */
@media (max-width: 768px) {
    .nav-menu {
        display: none;
        position: absolute;
        top: 100%;
        left: 0;
        right: 0;
        background: #2c3e50;
        flex-direction: column;
        padding: 1rem;
    }
    
    .nav-menu.active {
        display: flex;
    }
    
    .hamburger {
        display: flex;
    }
    
    .container {
        padding: 0 15px;
    }
    
    .post {
        padding: 1.5rem;
    }
    
    .footer-content {
        grid-template-columns: 1fr;
        text-align: center;
    }
}
```

**3. JavaScript fayl yarating (`static/js/main.js`):**
```javascript
// DOM ready
document.addEventListener('DOMContentLoaded', function() {
    // Mobile menu toggle
    const hamburger = document.querySelector('.hamburger');
    const navMenu = document.querySelector('.nav-menu');
    
    if (hamburger && navMenu) {
        hamburger.addEventListener('click', function() {
            navMenu.classList.toggle('active');
            hamburger.classList.toggle('active');
        });
    }
    
    // Form validation
    const forms = document.querySelectorAll('form');
    forms.forEach(form => {
        form.addEventListener('submit', function(e) {
            const requiredFields = form.querySelectorAll('[required]');
            let isValid = true;
            
            requiredFields.forEach(field => {
                if (!field.value.trim()) {
                    isValid = false;
                    field.style.borderColor = '#e74c3c';
                    
                    // Error message
                    let errorMsg = field.parentNode.querySelector('.error-message');
                    if (!errorMsg) {
                        errorMsg = document.createElement('div');
                        errorMsg.className = 'error-message';
                        errorMsg.style.color = '#e74c3c';
                        errorMsg.style.fontSize = '0.8rem';
                        errorMsg.style.marginTop = '0.25rem';
                        field.parentNode.appendChild(errorMsg);
                    }
                    errorMsg.textContent = 'Bu maydon to\'ldirilishi shart';
                } else {
                    field.style.borderColor = '#ddd';
                    const errorMsg = field.parentNode.querySelector('.error-message');
                    if (errorMsg) {
                        errorMsg.remove();
                    }
                }
            });
            
            if (!isValid) {
                e.preventDefault();
                showMessage('Iltimos, barcha maydonlarni to\'ldiring!', 'error');
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
    
    // Smooth scrolling for anchor links
    const anchorLinks = document.querySelectorAll('a[href^="#"]');
    anchorLinks.forEach(link => {
        link.addEventListener('click', function(e) {
            e.preventDefault();
            const target = document.querySelector(this.getAttribute('href'));
            if (target) {
                target.scrollIntoView({ 
                    behavior: 'smooth',
                    block: 'start'
                });
            }
        });
    });
    
    // Auto-hide messages
    const messages = document.querySelectorAll('.message');
    messages.forEach(message => {
        setTimeout(() => {
            message.style.opacity = '0';
            setTimeout(() => {
                message.remove();
            }, 300);
        }, 5000);
    });
    
    // Lazy loading for images
    const images = document.querySelectorAll('img[data-src]');
    const imageObserver = new IntersectionObserver((entries, observer) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                const img = entry.target;
                img.src = img.dataset.src;
                img.removeAttribute('data-src');
                observer.unobserve(img);
            }
        });
    });
    
    images.forEach(img => imageObserver.observe(img));
});

// Utility functions
function showMessage(message, type = 'info') {
    const messageContainer = document.createElement('div');
    messageContainer.className = `message message-${type}`;
    messageContainer.textContent = message;
    
    const container = document.querySelector('.container');
    if (container) {
        container.insertBefore(messageContainer, container.firstChild);
        
        setTimeout(() => {
            messageContainer.style.opacity = '0';
            setTimeout(() => {
                messageContainer.remove();
            }, 300);
        }, 5000);
    }
}

// AJAX request example
function loadMorePosts() {
    const button = document.querySelector('#load-more');
    if (button) {
        button.textContent = 'Yuklanmoqda...';
        button.disabled = true;
        
        fetch('/blog/api/posts/')
            .then(response => response.json())
            .then(data => {
                // Handle response
                console.log('Loaded posts:', data);
                button.textContent = 'Ko\'proq yuklash';
                button.disabled = false;
            })
            .catch(error => {
                console.error('Error:', error);
                button.textContent = 'Xatolik yuz berdi';
                button.disabled = false;
            });
    }
}

// Dark mode toggle
function toggleDarkMode() {
    document.body.classList.toggle('dark-mode');
    const isDark = document.body.classList.contains('dark-mode');
    localStorage.setItem('darkMode', isDark);
}

// Load dark mode preference
const darkMode = localStorage.getItem('darkMode');
if (darkMode === 'true') {
    document.body.classList.add('dark-mode');
}
```

### Natija
Static files tuzilishi yaratildi va CSS/JS fayllari qo'shildi.

---

## 📋 Mashg'ulot 3: Template'lar yaratish

### Vazifa
Blog uchun template'lar yarating va ularni base template bilan bog'lang.

### Qadamlar

**1. Post list template (`blog/templates/blog/post_list.html`):**
```html
{% extends 'base.html' %}

{% block title %}Barcha postlar{% endblock %}

{% block content %}
<div class="page-header">
    <h1>Blog postlar</h1>
    <p>{{ posts.count }} ta post topildi</p>
</div>

<div class="search-container">
    <input type="text" id="search-input" placeholder="Postlarni qidirish...">
</div>

<div class="posts-grid">
    {% for post in posts %}
        <article class="post">
            <header class="post-header">
                <h2><a href="{% url 'blog:post_detail' post.id %}">{{ post.title }}</a></h2>
                <div class="post-meta">
                    <span class="post-category">{{ post.category.name }}</span>
                    <span class="post-date">{{ post.created_at|date:"d.m.Y H:i" }}</span>
                </div>
            </header>
            
            <div class="post-content">
                <p>{{ post.content|truncatewords:50 }}</p>
            </div>
            
            <footer class="post-footer">
                <a href="{% url 'blog:post_detail' post.id %}" class="read-more">
                    Davomini o'qish →
                </a>
            </footer>
        </article>
    {% empty %}
        <div class="empty-state">
            <h3>Hozircha postlar yo'q</h3>
            <p>Birinchi postni yaratish uchun tizimga kiring.</p>
            {% if user.is_authenticated %}
                <a href="{% url 'blog:post_create' %}" class="btn">Yangi post yaratish</a>
            {% else %}
                <a href="{% url 'login' %}" class="btn">Tizimga kirish</a>
            {% endif %}
        </div>
    {% endfor %}
</div>

{% if is_paginated %}
    <div class="pagination">
        {% if page_obj.has_previous %}
            <a href="?page={{ page_obj.previous_page_number }}" class="btn">← Oldingi</a>
        {% endif %}
        
        <span class="page-info">
            {{ page_obj.number }} / {{ page_obj.paginator.num_pages }}
        </span>
        
        {% if page_obj.has_next %}
            <a href="?page={{ page_obj.next_page_number }}" class="btn">Keyingi →</a>
        {% endif %}
    </div>
{% endif %}
{% endblock %}
```

**2. Post detail template (`blog/templates/blog/post_detail.html`):**
```html
{% extends 'base.html' %}

{% block title %}{{ post.title }}{% endblock %}

{% block content %}
<article class="post-detail">
    <header class="post-header">
        <h1>{{ post.title }}</h1>
        <div class="post-meta">
            <span class="post-category">{{ post.category.name }}</span>
            <span class="post-date">{{ post.created_at|date:"d.m.Y H:i" }}</span>
            {% if post.updated_at != post.created_at %}
                <span class="post-updated">Yangilangan: {{ post.updated_at|date:"d.m.Y H:i" }}</span>
            {% endif %}
        </div>
    </header>
    
    <div class="post-content">
        {{ post.content|linebreaks }}
    </div>
    
    <footer class="post-footer">
        <div class="post-actions">
            {% if user.is_authenticated %}
                <a href="{% url 'blog:post_update' post.id %}" class="btn">Tahrirlash</a>
                <a href="{% url 'blog:post_delete' post.id %}" class="btn btn-danger">O'chirish</a>
            {% endif %}
            <a href="{% url 'blog:post_list' %}" class="btn">← Orqaga</a>
        </div>
    </footer>
</article>

{% if related_posts %}
    <section class="related-posts">
        <h3>O'xshash postlar</h3>
        <div class="posts-grid">
            {% for related_post in related_posts %}
                <article class="post">
                    <h4><a href="{% url 'blog:post_detail' related_post.id %}">{{ related_post.title }}</a></h4>
                    <p>{{ related_post.content|truncatewords:20 }}</p>
                </article>
            {% endfor %}
        </div>
    </section>
{% endif %}
{% endblock %}
```

**3. Post form template (`blog/templates/blog/post_form.html`):**
```html
{% extends 'base.html' %}

{% block title %}
    {% if form.instance.pk %}Postni tahrirlash{% else %}Yangi post{% endif %}
{% endblock %}

{% block content %}
<div class="form-container">
    <h1>
        {% if form.instance.pk %}
            "{{ form.instance.title }}" ni tahrirlash
        {% else %}
            Yangi post yaratish
        {% endif %}
    </h1>
    
    <form method="POST" enctype="multipart/form-data">
        {% csrf_token %}
        
        <div class="form-group">
            <label for="{{ form.title.id_for_label }}">Sarlavha *</label>
            {{ form.title }}
            {% if form.title.errors %}
                <div class="error-message">{{ form.title.errors.0 }}</div>
            {% endif %}
        </div>
        
        <div class="form-group">
            <label for="{{ form.content.id_for_label }}">Matn *</label>
            {{ form.content }}
            {% if form.content.errors %}
                <div class="error-message">{{ form.content.errors.0 }}</div>
            {% endif %}
        </div>
        
        <div class="form-group">
            <label for="{{ form.category.id_for_label }}">Kategoriya *</label>
            {{ form.category }}
            {% if form.category.errors %}
                <div class="error-message">{{ form.category.errors.0 }}</div>
            {% endif %}
        </div>
        
        <div class="form-group">
            <label>
                {{ form.is_published }} Nashr etilgan
            </label>
        </div>
        
        <div class="form-actions">
            <button type="submit" class="btn">
                {% if form.instance.pk %}Saqlash{% else %}Yaratish{% endif %}
            </button>
            <a href="{% url 'blog:post_list' %}" class="btn btn-secondary">Bekor qilish</a>
        </div>
    </form>
</div>
{% endblock %}
```

**4. About template (`blog/templates/blog/about.html`):**
```html
{% extends 'base.html' %}

{% block title %}Haqida{% endblock %}

{% block content %}
<div class="about-page">
    <h1>Biz haqida</h1>
    
    <div class="about-content">
        <p>Bu blog Django framework yordamida yaratilgan va zamonaviy web texnologiyalarini o'rgatadi.</p>
        
        <h2>Bizning maqsadlarimiz</h2>
        <ul>
            <li>Dasturlash bo'yicha foydali ma'lumotlar taqdim etish</li>
            <li>Zamonaviy web texnologiyalarini o'rgatish</li>
            <li>Dasturchilar jamiyatini rivojlantirish</li>
        </ul>
        
        <h2>Texnologiyalar</h2>
        <div class="tech-stack">
            <div class="tech-item">
                <h3>Backend</h3>
                <ul>
                    <li>Django</li>
                    <li>Python</li>
                    <li>PostgreSQL</li>
                </ul>
            </div>
            
            <div class="tech-item">
                <h3>Frontend</h3>
                <ul>
                    <li>HTML5</li>
                    <li>CSS3</li>
                    <li>JavaScript</li>
                </ul>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### Natija
Barcha template'lar yaratildi va base template bilan bog'landi.

---

## 📋 Mashg'ulot 4: Custom template filters

### Vazifa
O'zingizning template filter'larini yarating.

### Qadamlar

**1. Templatetags papkasini yarating:**
```bash
mkdir blog/templatetags
touch blog/templatetags/__init__.py
```

**2. Custom filters yarating (`blog/templatetags/blog_extras.py`):**
```python
from django import template
from django.utils.safestring import mark_safe
from django.utils.html import escape
import re

register = template.Library()

@register.filter
def highlight_search(text, search_term):
    """Qidiruv so'zini yorqin qilish"""
    if not search_term:
        return text
    
    pattern = re.compile(f'({re.escape(search_term)})', re.IGNORECASE)
    highlighted = pattern.sub(r'<mark>\1</mark>', text)
    return mark_safe(highlighted)

@register.filter
def word_count(text):
    """So'zlar sonini hisoblash"""
    return len(text.split())

@register.filter
def reading_time(text):
    """O'qish vaqtini hisoblash (o'rtacha 200 so'z/daqiqa)"""
    words = len(text.split())
    minutes = max(1, words // 200)
    return f"{minutes} daqiqa"

@register.filter
def truncate_smart(text, length=100):
    """Aqlli kesish - so'zni yarimda kesmaydi"""
    if len(text) <= length:
        return text
    
    truncated = text[:length]
    last_space = truncated.rfind(' ')
    
    if last_space > length * 0.8:  # 80% dan ko'p bo'lsa
        return truncated[:last_space] + '...'
    else:
        return truncated + '...'

@register.filter
def format_number(number):
    """Raqamlarni formatlash"""
    if number >= 1000000:
        return f"{number/1000000:.1f}M"
    elif number >= 1000:
        return f"{number/1000:.1f}K"
    else:
        return str(number)

@register.filter
def time_ago(date):
    """Vaqtni "qachon" formatida ko'rsatish"""
    from django.utils import timezone
    from datetime import timedelta
    
    now = timezone.now()
    diff = now - date
    
    if diff.days > 0:
        if diff.days == 1:
            return "Kecha"
        elif diff.days < 7:
            return f"{diff.days} kun oldin"
        elif diff.days < 30:
            weeks = diff.days // 7
            return f"{weeks} hafta oldin"
        else:
            months = diff.days // 30
            return f"{months} oy oldin"
    else:
        hours = diff.seconds // 3600
        if hours > 0:
            return f"{hours} soat oldin"
        else:
            minutes = diff.seconds // 60
            return f"{minutes} daqiqa oldin"

@register.filter
def markdown_to_html(text):
    """Markdown ni HTML ga o'tkazish (oddiy)"""
    # Bold
    text = re.sub(r'\*\*(.*?)\*\*', r'<strong>\1</strong>', text)
    # Italic
    text = re.sub(r'\*(.*?)\*', r'<em>\1</em>', text)
    # Links
    text = re.sub(r'\[([^\]]+)\]\(([^)]+)\)', r'<a href="\2">\1</a>', text)
    # Line breaks
    text = text.replace('\n', '<br>')
    
    return mark_safe(text)
```

**3. Template'da ishlatish:**
```html
{% load blog_extras %}

<!-- Post list template'da -->
<div class="post-meta">
    <span>{{ post.created_at|time_ago }}</span>
    <span>{{ post.content|word_count }} so'z</span>
    <span>{{ post.content|reading_time }} o'qish</span>
</div>

<!-- Search results'da -->
<p>{{ post.content|highlight_search:query|truncate_smart:200 }}</p>

<!-- Post detail'da -->
<div class="post-content">
    {{ post.content|markdown_to_html|linebreaks }}
</div>
```

### Natija
Custom template filter'lar yaratildi va ishlatildi.

---

## 📋 Mashg'ulot 5: Template context processors

### Vazifa
Global context processor yarating.

### Qadamlar

**1. Context processor yarating (`blog/context_processors.py`):**
```python
from .models import Category, Post
from django.utils import timezone
from datetime import timedelta

def global_context(request):
    """Global context barcha template'larga qo'shiladi"""
    context = {
        'site_name': 'Django Blog',
        'site_description': 'Zamonaviy web texnologiyalarini o\'rgatuvchi blog',
        'current_year': timezone.now().year,
    }
    
    # Kategoriyalar
    try:
        context['categories'] = Category.objects.all()
    except:
        context['categories'] = []
    
    # So'nggi postlar
    try:
        context['recent_posts'] = Post.objects.filter(
            is_published=True
        ).order_by('-created_at')[:5]
    except:
        context['recent_posts'] = []
    
    # Mashhur postlar (so'nggi 30 kundagi)
    try:
        thirty_days_ago = timezone.now() - timedelta(days=30)
        context['popular_posts'] = Post.objects.filter(
            is_published=True,
            created_at__gte=thirty_days_ago
        ).order_by('-created_at')[:3]
    except:
        context['popular_posts'] = []
    
    # Statistika
    try:
        context['total_posts'] = Post.objects.filter(is_published=True).count()
        context['total_categories'] = Category.objects.count()
    except:
        context['total_posts'] = 0
        context['total_categories'] = 0
    
    return context
```

**2. Settings.py ga qo'shing:**
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

**3. Template'da ishlatish:**
```html
<!-- Base template'da -->
<title>{% block title %}{{ site_name }}{% endblock %}</title>
<meta name="description" content="{{ site_description }}">

<!-- Sidebar'da -->
<div class="sidebar">
    <h3>Kategoriyalar</h3>
    <ul>
        {% for category in categories %}
            <li><a href="{% url 'blog:category_posts' category.name %}">{{ category.name }}</a></li>
        {% endfor %}
    </ul>
    
    <h3>So'nggi postlar</h3>
    <ul>
        {% for post in recent_posts %}
            <li><a href="{% url 'blog:post_detail' post.id %}">{{ post.title }}</a></li>
        {% endfor %}
    </ul>
    
    <div class="stats">
        <p>Jami: {{ total_posts }} ta post</p>
        <p>Kategoriyalar: {{ total_categories }} ta</p>
    </div>
</div>
```

### Natija
Global context processor yaratildi va ishlatildi.

---

## ✅ Tekshirish ro'yxati

- [ ] Template tuzilishi yaratildi
- [ ] Base template sozlandi
- [ ] Static files tuzilishi yaratildi
- [ ] CSS va JS fayllari qo'shildi
- [ ] Template'lar yaratildi va bog'landi
- [ ] Custom template filter'lar yaratildi
- [ ] Context processor yaratildi
- [ ] Responsive dizayn sozlandi
- [ ] Barcha sahifalar ishlaydi

---

## 🎉 Natija

Siz Django Templates va Static Files bilan to'liq ishlashni o'rgandingiz:
- Template tuzilishi va inheritance
- Static files (CSS, JS, rasm)
- Template variables, tags, filters
- Custom template filters
- Context processors
- Responsive dizayn

**Keyingi dars:** Django Forms va User Management 