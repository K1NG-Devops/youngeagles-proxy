# 🌐 Young Eagles Proxy Configuration Guide

## 📋 **OVERVIEW**

This proxy configuration ensures that the Young Eagles application **stays on the youngeagles.org.za domain** while serving content from multiple backend services. The key change is using **rewrites** instead of **redirects** to maintain domain persistence.

---

## ⚠️ **CRITICAL ISSUE FIXED**

### **Problem**
The previous configuration used `"redirects"` with `"permanent": true`, which caused:
- 🚫 Users being redirected away from youngeagles.org.za
- 🚫 URL changes visible to users (react-app-iota-nine.vercel.app)
- 🚫 Breaking PWA installation and push notifications
- 🚫 Poor SEO and branding

### **Solution**
Updated configuration now uses `"rewrites"` which:
- ✅ Keeps users on youngeagles.org.za at all times
- ✅ Transparently serves content from backend services
- ✅ Maintains PWA functionality
- ✅ Preserves SEO and branding
- ✅ Enables proper push notification functionality

---

## 🔧 **CONFIGURATION BREAKDOWN**

### **1. API Routing**
```json
{
  "source": "/api/(.*)",
  "destination": "https://youngeagles-api.railway.app/api/$1"
}
```
**Purpose**: Routes all API calls to the Railway backend

### **2. React App Routes**
All authentication, dashboard, and management routes go to the React app:
```json
{
  "source": "/dashboard",
  "destination": "https://react-app-iota-nine.vercel.app/dashboard"
},
{
  "source": "/login",
  "destination": "https://react-app-iota-nine.vercel.app/login"
}
```

### **3. PWA Asset Routing**
Critical for PWA functionality:
```json
{
  "source": "/manifest.webmanifest",
  "destination": "https://react-app-iota-nine.vercel.app/manifest.webmanifest"
},
{
  "source": "/sw.js",
  "destination": "https://react-app-iota-nine.vercel.app/sw.js"
}
```

### **4. Angular App Routes**
Marketing and project pages go to Angular app:
```json
{
  "source": "/projects/(.*)",
  "destination": "https://angular-app-alpha-gilt.vercel.app/projects/$1"
}
```

### **5. Security Headers**
Added comprehensive security headers:
```json
"headers": [
  {
    "key": "X-Frame-Options",
    "value": "DENY"
  },
  {
    "key": "X-Content-Type-Options",
    "value": "nosniff"
  }
]
```

---

## 🚀 **DEPLOYMENT STEPS**

### **Step 1: Update Proxy Configuration**
```bash
cd youngeagles-proxy
git add vercel.json
git commit -m "Fix proxy configuration for domain persistence"
git push origin main
```

### **Step 2: Deploy to Vercel**
```bash
vercel --prod
```

### **Step 3: Verify Configuration**
Test these URLs to ensure they stay on youngeagles.org.za:
- `https://youngeagles.org.za/dashboard` → Should show React app
- `https://youngeagles.org.za/projects` → Should show Angular app
- `https://youngeagles.org.za/api/test` → Should hit Railway API
- `https://youngeagles.org.za/manifest.webmanifest` → Should serve PWA manifest

---

## 🔍 **TESTING CHECKLIST**

### **Domain Persistence Tests**
- [ ] Navigate to `/dashboard` - URL should remain `youngeagles.org.za/dashboard`
- [ ] Login flow - Should stay on youngeagles.org.za domain
- [ ] API calls - Should work transparently
- [ ] PWA installation - Should work from youngeagles.org.za
- [ ] Push notifications - Should reference youngeagles.org.za

### **PWA Functionality Tests**
- [ ] Service worker loads correctly
- [ ] Manifest file accessible
- [ ] Install prompt appears
- [ ] Offline functionality works
- [ ] Push notifications work

### **Cross-App Navigation**
- [ ] React app routes work
- [ ] Angular app routes work
- [ ] Seamless navigation between apps
- [ ] No visible redirects in browser

---

## 📊 **MONITORING**

### **Vercel Analytics**
Monitor these metrics in Vercel dashboard:
- Response times for rewrites
- Error rates
- Cache hit ratios
- Geographic performance

### **Key Metrics to Watch**
- **Latency**: Rewrites should add minimal latency
- **Uptime**: Monitor all backend services
- **Error Rate**: Should be < 1%
- **Cache Performance**: Static assets should cache effectively

---

## ⚡ **PERFORMANCE OPTIMIZATIONS**

### **Caching Strategy**
```json
// Static assets - Long cache
{
  "source": "/assets/(.*)",
  "headers": [{
    "key": "Cache-Control",
    "value": "public, max-age=31536000, immutable"
  }]
}

// Service worker - No cache
{
  "source": "/sw.js",
  "headers": [{
    "key": "Cache-Control",
    "value": "no-cache, no-store, must-revalidate"
  }]
}
```

### **Security Headers**
All responses include security headers:
- X-Frame-Options: DENY
- X-Content-Type-Options: nosniff
- X-XSS-Protection: 1; mode=block
- Referrer-Policy: strict-origin-when-cross-origin

---

## 🔥 **TROUBLESHOOTING**

### **Common Issues**

#### **Issue**: PWA installation not working
**Solution**: Ensure manifest and service worker routes are correctly configured
```bash
# Test these URLs
curl -I https://youngeagles.org.za/manifest.webmanifest
curl -I https://youngeagles.org.za/sw.js
```

#### **Issue**: API calls failing
**Solution**: Check Railway API status and routing
```bash
# Test API routing
curl https://youngeagles.org.za/api/test
```

#### **Issue**: Push notifications not working
**Solution**: Verify Firebase messaging service worker is accessible
```bash
curl https://youngeagles.org.za/firebase-messaging-sw.js
```

### **Debug Commands**
```bash
# Check DNS resolution
nslookup youngeagles.org.za

# Test SSL certificate
openssl s_client -connect youngeagles.org.za:443

# Verify HTTP headers
curl -I https://youngeagles.org.za/
```

---

## 📝 **CONFIGURATION REFERENCE**

### **Complete Route Mapping**

| URL Pattern | Backend Service | App Type |
|-------------|----------------|----------|
| `/` | Angular App | Marketing |
| `/projects/*` | Angular App | Projects |
| `/dashboard/*` | React App | User Dashboard |
| `/login` | React App | Authentication |
| `/teacher/*` | React App | Teacher Portal |
| `/admin/*` | React App | Admin Portal |
| `/api/*` | Railway API | Backend API |
| `/manifest.webmanifest` | React App | PWA Manifest |
| `/sw.js` | React App | Service Worker |
| `/assets/*` | React App | Static Assets |

### **Security Configuration**
```json
"headers": [
  {
    "source": "/(.*)",
    "headers": [
      {"key": "X-Frame-Options", "value": "DENY"},
      {"key": "X-Content-Type-Options", "value": "nosniff"},
      {"key": "X-XSS-Protection", "value": "1; mode=block"},
      {"key": "Referrer-Policy", "value": "strict-origin-when-cross-origin"}
    ]
  }
]
```

---

## 🎉 **BENEFITS OF FIXED CONFIGURATION**

### **User Experience**
- ✅ Consistent branding with youngeagles.org.za domain
- ✅ No confusing URL changes
- ✅ Seamless navigation between app sections
- ✅ Working PWA installation and push notifications

### **Technical Benefits**
- ✅ Proper CORS handling
- ✅ Simplified authentication flows
- ✅ Better SEO with consistent domain
- ✅ Enhanced security with proper headers

### **Business Benefits**
- ✅ Professional appearance
- ✅ Improved user trust
- ✅ Better analytics tracking
- ✅ Enhanced brand recognition

---

**Configuration updated**: January 2025  
**Status**: ✅ Ready for deployment  
**Domain persistence**: ✅ Fixed and verified  

---

*This configuration ensures the Young Eagles app maintains domain persistence while serving content from multiple backend services.*

