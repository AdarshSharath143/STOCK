# 🚀 Combined Deployment Guide: Backend + Frontend Together

Deploy both your Flask backend and React frontend together on Render as a single service.

---

## ✅ Step 1: Code is Already Updated!

I've already updated your `backend/app.py` to:
- ✅ Serve static files from `frontend/dist`
- ✅ Handle React Router (catch-all route)
- ✅ Keep all your existing API routes

---

## 📋 Step 2: Update Render Configuration

### In Render Dashboard:

1. **Go to your Web Service** (or create new one)

2. **Settings to configure:**

```
Name: stock-trend-prediction
(or your preferred name)

Region: Choose closest to you

Branch: main
(or your default branch)

Root Directory: backend
← IMPORTANT: Point to backend folder!

Build Command:
cd ../frontend && npm install && npm run build
← This builds React first, then Flask can serve it

Start Command:
gunicorn app:app --bind 0.0.0.0:$PORT
← Or use: waitress-serve --host=0.0.0.0 --port=$PORT app:app
```

3. **Environment Variables:**

Add these:
```
PERPLEXITY_API_KEY=your_api_key_here
OPENWEATHER_API_KEY=your_openweather_key (optional)
JWT_SECRET_KEY=generate_random_secret_key
FLASK_ENV=production
PORT=10000
```

**Note:** You don't need `VITE_API_URL` anymore since everything is on the same domain!

4. **Save and Deploy**

---

## 📝 Step 3: Verify Your Structure

Your project structure should look like:

```
STOCK-TREND-PREDICTION/
├── backend/
│   ├── app.py          ← Updated to serve static files
│   ├── requirements.txt ← Has waitress added
│   ├── Procfile        ← Uses gunicorn
│   └── ...
│
└── frontend/
    ├── src/
    ├── package.json
    ├── vite.config.js
    └── dist/           ← Will be created by build
```

---

## ✅ Step 4: What Changed

### In `backend/app.py`:

1. **Added import:**
   ```python
   from flask import Flask, request, jsonify, send_from_directory
   ```

2. **Updated Flask initialization:**
   ```python
   static_folder = os.path.join(os.path.dirname(os.path.dirname(__file__)), 'frontend', 'dist')
   app = Flask(__name__, static_folder=static_folder, static_url_path="")
   ```

3. **Added catch-all route** (at the end, before `if __name__ == '__main__'`):
   ```python
   @app.route("/", defaults={"path": ""})
   @app.route("/<path:path>")
   def serve_frontend(path):
       # Serves React app for all non-API routes
   ```

### In `backend/requirements.txt`:

- ✅ Added `waitress` (alternative to gunicorn)

---

## 🔧 Step 5: Build Process

### What Happens on Render:

1. **Build Command runs:**
   ```bash
   cd ../frontend && npm install && npm run build
   ```
   - Changes to frontend directory
   - Installs npm packages
   - Builds React app to `frontend/dist/`

2. **Start Command runs:**
   ```bash
   gunicorn app:app --bind 0.0.0.0:$PORT
   ```
   - Flask starts
   - Serves API at `/api/*`
   - Serves React at `/` and all other routes

---

## ✅ Step 6: Testing

### After Deployment:

1. **Frontend (React):**
   ```
   https://your-app.onrender.com
   ```
   - Should load your React app
   - All routes should work (client-side routing)

2. **Backend API:**
   ```
   https://your-app.onrender.com/api/health
   ```
   - Should return: `{"status": "healthy", "message": "FinSight AI Backend is running"}`

3. **API Endpoints:**
   ```
   https://your-app.onrender.com/api/strategy?name=ema_crossover&symbol=AAPL
   https://your-app.onrender.com/api/predict?model=lstm&symbol=TSLA
   ```

---

## 🎯 Step 7: Benefits of Combined Deployment

✅ **No CORS Issues**
- Same origin for frontend and backend
- No CORS configuration needed (though it's there as backup)

✅ **One URL**
- Everything on one domain
- Simpler deployment

✅ **Faster Cold Starts**
- Single service instead of two
- Less overhead

✅ **Cost Effective**
- One Render service instead of two
- Free tier goes further

---

## 🐛 Troubleshooting

### Issue: "Frontend not built" Error

**Problem:** `frontend/dist` doesn't exist

**Solution:**
- Check build command: `cd ../frontend && npm install && npm run build`
- Check build logs in Render
- Ensure `frontend/package.json` exists

### Issue: API Routes Return 404

**Problem:** All routes go to React app

**Solution:**
- Check that your API routes are defined BEFORE the catch-all route
- API routes should start with `/api/`
- The catch-all route checks for `path.startswith('api/')` and returns 404

### Issue: React Router Routes Don't Work

**Problem:** Direct URL access returns 404

**Solution:**
- The catch-all route should serve `index.html` for non-API routes
- Verify `frontend/dist/index.html` exists after build

### Issue: Static Assets (JS/CSS) Not Loading

**Problem:** 404 for `.js` and `.css` files

**Solution:**
- Check that build creates files in `frontend/dist/assets/`
- The catch-all route should serve static files if they exist
- Verify build command completes successfully

---

## 📝 Step 8: Update Frontend Config (Optional)

Since everything is on the same domain now, you can simplify your frontend config:

### Option 1: Keep it as is (works fine)
- `config.js` can still use environment variables
- But API calls can just use relative paths

### Option 2: Simplify (recommended)

In `frontend/src/config.js`:
```javascript
// Since backend and frontend are on same domain now
export const API_BASE_URL = '';  // Empty = same domain
export const API_URL = '';
```

And in `frontend/src/main.jsx`:
```javascript
// Can simplify since same domain
axios.defaults.baseURL = API_URL || '';  // Empty = relative URLs
```

But keeping it as is also works fine!

---

## ✅ Step 9: Deploy Checklist

Before deploying, ensure:

- [ ] `backend/app.py` has static folder configuration
- [ ] `backend/app.py` has catch-all route
- [ ] `backend/requirements.txt` has `waitress` (or use gunicorn)
- [ ] `frontend/package.json` has build script
- [ ] Project structure is correct (backend/ and frontend/ folders)
- [ ] Environment variables are set in Render
- [ ] Root Directory is set to `backend`
- [ ] Build Command is: `cd ../frontend && npm install && npm run build`
- [ ] Start Command is: `gunicorn app:app --bind 0.0.0.0:$PORT`

---

## 🎉 Step 10: Done!

After deployment:

1. ✅ Your React app loads at: `https://your-app.onrender.com`
2. ✅ Your API works at: `https://your-app.onrender.com/api/*`
3. ✅ No CORS issues
4. ✅ Everything on one domain
5. ✅ Single Render service

---

## 🔄 Future Deployments

1. **Push code to GitHub**
2. **Render auto-deploys** (if enabled)
3. **Build command runs** → builds React
4. **Start command runs** → starts Flask
5. **Done!** ✨

---

## 📞 Need Help?

Check Render logs if build fails:
1. Go to Render dashboard
2. Click your service
3. Click "Logs" tab
4. Check for errors

Common issues:
- Build command path incorrect
- npm/node version mismatch
- Missing dependencies

---

**That's it! Your app should now work as a single combined service!** 🚀

