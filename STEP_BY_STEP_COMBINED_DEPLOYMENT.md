# 🚀 Step-by-Step: Deploy Backend + Frontend Together on Render

## ✅ Step 1: Code Updates (ALREADY DONE!)

I've updated your code:
- ✅ `backend/app.py` - Now serves static files from `frontend/dist`
- ✅ `backend/app.py` - Added catch-all route for React Router
- ✅ `backend/requirements.txt` - Added `waitress`

**You don't need to do anything here!** ✅

---

## 📋 Step 2: Push Code to GitHub

1. **Open terminal in your project folder**
2. **Commit and push:**

```bash
cd STOCK-TREND-PREDICTION
git add .
git commit -m "Add combined deployment support - Flask serves React"
git push
```

---

## 🌐 Step 3: Configure Render Web Service

### A. Go to Render Dashboard

1. Go to [render.com](https://render.com)
2. Sign in to your account

### B. Create/Update Web Service

1. **If creating new:**
   - Click "New +" → "Web Service"
   - Connect your GitHub repository

2. **If updating existing:**
   - Click on your existing Web Service
   - Go to "Settings" tab

### C. Configure These Settings:

```
Name: stock-trend-prediction
(or any name you prefer)

Region: Choose closest to you

Branch: main
(or your default branch)

Root Directory: backend
← IMPORTANT: Point to backend folder!
```

**Build Command:**
```
cd ../frontend && npm install && npm run build
```

**Start Command:**
```
gunicorn app:app --bind 0.0.0.0:$PORT
```

**OR if you prefer waitress:**
```
waitress-serve --host=0.0.0.0 --port=$PORT app:app
```

---

## 🔧 Step 4: Add Environment Variables

In Render dashboard, go to "Environment" section and add:

```
PERPLEXITY_API_KEY=your_perplexity_api_key_here
OPENWEATHER_API_KEY=your_openweather_key (optional)
JWT_SECRET_KEY=generate_random_secret_key_here
FLASK_ENV=production
```

**Generate JWT Secret Key:**
```python
python -c "import secrets; print(secrets.token_hex(32))"
```

**Note:** You DON'T need `VITE_API_URL` anymore since everything is on the same domain!

---

## 🚀 Step 5: Deploy

1. **Click "Create Web Service"** (or "Save Changes" if updating)
2. **Wait for deployment** (5-10 minutes)
   - Build command runs: builds React → creates `frontend/dist/`
   - Start command runs: starts Flask → serves both API and React

3. **Check deployment logs**
   - Watch for: "✓ built in X seconds" (React build)
   - Watch for: "Application startup complete" (Flask)

---

## ✅ Step 6: Verify Deployment

### A. Test Backend API:

1. **Health Check:**
   ```
   https://your-app.onrender.com/api/health
   ```
   Should return: `{"status": "healthy", "message": "FinSight AI Backend is running"}`

2. **Test API Endpoint:**
   ```
   https://your-app.onrender.com/api/strategies
   ```
   Should return list of strategies

### B. Test Frontend:

1. **Visit your app:**
   ```
   https://your-app.onrender.com
   ```
   - Should load your React app
   - Homepage should appear

2. **Test React Router:**
   - Navigate to different pages (Dashboard, Strategies, etc.)
   - All should work!

3. **Test API Calls:**
   - Try searching for a stock
   - Try running a strategy
   - Check browser console (F12) for errors
   - All API calls should work without CORS issues!

---

## 🎯 Step 7: How It Works

### Build Process:

```
1. Render runs: cd ../frontend && npm install && npm run build
   → Installs npm packages
   → Builds React app
   → Creates frontend/dist/ folder with:
      - index.html
      - assets/*.js
      - assets/*.css

2. Render runs: gunicorn app:app --bind 0.0.0.0:$PORT
   → Flask starts
   → Serves API routes: /api/*
   → Serves React app: / and all other routes
```

### Request Flow:

```
User visits: https://your-app.onrender.com/strategies

1. Request goes to Flask
2. Flask checks: Is it /api/*? → No
3. Flask checks: Does frontend/dist/strategies exist? → No
4. Flask serves: frontend/dist/index.html
5. React loads and handles routing
6. React shows /strategies page ✅
```

```
User clicks "Analyze" → API call to /api/strategy

1. Request goes to Flask
2. Flask checks: Is it /api/*? → Yes
3. Flask routes to: @app.route('/api/strategy')
4. Returns JSON response ✅
```

---

## 🐛 Step 8: Troubleshooting

### Problem: "Frontend not built" Error

**Solution:**
1. Check build command is correct: `cd ../frontend && npm install && npm run build`
2. Check build logs in Render
3. Verify `frontend/package.json` exists
4. Check Node.js version (needs 18+)

### Problem: API Routes Return 404

**Solution:**
1. Ensure API routes are defined BEFORE catch-all route (they are)
2. API routes should start with `/api/`
3. Check Render logs for errors

### Problem: React Routes Don't Work (404)

**Solution:**
1. Verify `frontend/dist/index.html` exists after build
2. Check that catch-all route serves `index.html`
3. Check browser console for errors

### Problem: Static Files (JS/CSS) Not Loading

**Solution:**
1. Check build created files in `frontend/dist/assets/`
2. Check Render logs for build errors
3. Verify static folder path in `app.py`

---

## ✅ Step 9: Success Checklist

After deployment, verify:

- [ ] ✅ Backend API works: `https://your-app.onrender.com/api/health`
- [ ] ✅ Frontend loads: `https://your-app.onrender.com`
- [ ] ✅ React Router works (navigate between pages)
- [ ] ✅ API calls work (no CORS errors)
- [ ] ✅ All features work (strategies, predictions, etc.)
- [ ] ✅ No console errors in browser

---

## 🎉 Step 10: You're Done!

Now you have:

✅ **One URL** for everything
✅ **No CORS issues** (same origin)
✅ **Faster deployment** (single service)
✅ **Cost effective** (one Render service)

---

## 📝 Summary of Settings

| Setting | Value |
|---------|-------|
| **Root Directory** | `backend` |
| **Build Command** | `cd ../frontend && npm install && npm run build` |
| **Start Command** | `gunicorn app:app --bind 0.0.0.0:$PORT` |
| **Environment Variables** | `PERPLEXITY_API_KEY`, `JWT_SECRET_KEY`, `FLASK_ENV=production` |

---

## 🔄 Future Updates

1. **Make changes to code**
2. **Push to GitHub:**
   ```bash
   git add .
   git commit -m "Your changes"
   git push
   ```
3. **Render auto-deploys** (if enabled)
4. **Wait for rebuild** (2-5 minutes)
5. **Done!** ✨

---

**That's it! Your combined deployment is ready!** 🚀

For detailed information, see `COMBINED_DEPLOYMENT_GUIDE.md`

