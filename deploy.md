# دليل النشر والتشغيل

## النشر على الخوادم

### 1. النشر على GitHub Pages

```bash
# إنشاء مستودع جديد
git init
git add .
git commit -m "Initial commit"

# رفع إلى GitHub
git remote add origin https://github.com/username/alwathaq-governance.git
git push -u origin main

# تفعيل GitHub Pages من إعدادات المستودع
```

### 2. النشر على Netlify

1. ارفع الملفات إلى Netlify
2. أو اربط المستودع مباشرة
3. الموقع سيكون متاحاً على: `https://your-site.netlify.app`

### 3. النشر على Vercel

```bash
# تثبيت Vercel CLI
npm i -g vercel

# النشر
vercel
```

### 4. النشر على خادم تقليدي

```bash
# رفع الملفات عبر FTP/SFTP
# أو استخدام rsync
rsync -avz ./ user@server:/var/www/html/
```

## إعدادات الخادم

### Apache (.htaccess)
```apache
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.html [QSA,L]

# ضغط الملفات
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/plain
    AddOutputFilterByType DEFLATE text/html
    AddOutputFilterByType DEFLATE text/xml
    AddOutputFilterByType DEFLATE text/css
    AddOutputFilterByType DEFLATE application/xml
    AddOutputFilterByType DEFLATE application/xhtml+xml
    AddOutputFilterByType DEFLATE application/rss+xml
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE application/x-javascript
</IfModule>

# Cache Control
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresByType text/css "access plus 1 year"
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
</IfModule>
```

### Nginx
```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # ضغط الملفات
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/xml+rss application/json;

    # Cache Control
    location ~* \.(css|js|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

## تحسين الأداء

### 1. ضغط الصور
```bash
# استخدام ImageOptim أو TinyPNG
# أو استخدام أدوات سطر الأوامر
npm install -g imagemin-cli
imagemin images/* --out-dir=optimized/
```

### 2. تحسين CSS و JavaScript
```bash
# ضغط CSS
npm install -g clean-css-cli
cleancss -o styles.min.css styles.css

# ضغط JavaScript
npm install -g uglify-js
uglifyjs script.js -o script.min.js
```

### 3. إضافة Service Worker
```javascript
// sw.js
const CACHE_NAME = 'alwathaq-v1';
const urlsToCache = [
  '/',
  '/index.html',
  '/styles.css',
  '/script.js'
];

self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => cache.addAll(urlsToCache))
  );
});
```

## مراقبة الأداء

### 1. Google Analytics
```html
<!-- إضافة إلى index.html -->
<script async src="https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'GA_MEASUREMENT_ID');
</script>
```

### 2. Google PageSpeed Insights
- قم بزيارة: https://pagespeed.web.dev/
- أدخل رابط موقعك
- اتبع التوصيات لتحسين الأداء

### 3. WebPageTest
- قم بزيارة: https://www.webpagetest.org/
- اختبر سرعة الموقع من مواقع مختلفة

## الأمان

### 1. HTTPS
```bash
# الحصول على شهادة SSL مجانية من Let's Encrypt
sudo certbot --apache -d your-domain.com
```

### 2. Headers الأمان
```apache
# إضافة إلى .htaccess
Header always set X-Content-Type-Options nosniff
Header always set X-Frame-Options DENY
Header always set X-XSS-Protection "1; mode=block"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
```

### 3. Content Security Policy
```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' 'unsafe-inline' https://www.googletagmanager.com; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com;">
```

## النسخ الاحتياطي

### 1. نسخ احتياطي تلقائي
```bash
#!/bin/bash
# backup.sh
DATE=$(date +%Y%m%d_%H%M%S)
tar -czf backup_$DATE.tar.gz /var/www/html/
aws s3 cp backup_$DATE.tar.gz s3://your-backup-bucket/
```

### 2. جدولة النسخ الاحتياطي
```bash
# إضافة إلى crontab
0 2 * * * /path/to/backup.sh
```

## مراقبة الأخطاء

### 1. Sentry
```html
<script src="https://browser.sentry-cdn.com/7.x.x/bundle.min.js" integrity="sha384-..." crossorigin="anonymous"></script>
<script>
  Sentry.init({
    dsn: "YOUR_DSN",
    environment: "production"
  });
</script>
```

### 2. Log Monitoring
```bash
# مراقبة سجلات الخادم
tail -f /var/log/apache2/access.log
tail -f /var/log/nginx/access.log
```

## اختبار الموقع

### 1. اختبار التوافق
- Chrome DevTools
- Firefox Developer Tools
- Safari Web Inspector
- Edge DevTools

### 2. اختبار التجاوب
- Chrome DevTools Device Mode
- BrowserStack
- LambdaTest

### 3. اختبار الأداء
- Lighthouse
- WebPageTest
- GTmetrix

## الصيانة الدورية

### 1. تحديث المكتبات
```bash
# فحص التحديثات
npm outdated

# تحديث التبعيات
npm update
```

### 2. فحص الروابط المكسورة
```bash
# استخدام أداة فحص الروابط
npm install -g broken-link-checker
blc https://your-domain.com
```

### 3. فحص الأمان
```bash
# فحص الثغرات الأمنية
npm audit
npm audit fix
```

## الدعم الفني

في حالة مواجهة أي مشاكل:

1. راجع سجلات الخادم
2. تحقق من إعدادات DNS
3. تأكد من صحة شهادة SSL
4. راجع إعدادات Firewall
5. تواصل مع فريق الدعم الفني

---

**ملاحظة**: تأكد من اختبار الموقع بشكل شامل قبل النشر النهائي. 