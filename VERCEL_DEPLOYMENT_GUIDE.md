# 🚀 Vercel Deployment Guide - Google OAuth Fix

## Issue: Google Sign-In Authentication Server Error

This guide provides a complete solution to fix the Google OAuth authentication error after deploying to Vercel.

---

## 🔧 Root Causes

1. **Hardcoded API URL** - Frontend points to `localhost:5000` instead of production API
2. **Missing CORS configuration** - Backend doesn't allow frontend domain
3. **Incomplete error handling** - Google OAuth endpoint missing proper error logs
4. **Missing environment variables** - Database and OAuth credentials not configured

---

## ✅ Fixed Issues

### Backend Improvements
- ✅ Enhanced CORS with explicit origin configuration
- ✅ Improved Google OAuth endpoint with detailed logging
- ✅ Added request/response interceptors for debugging
- ✅ Better error messages for diagnosis

### Frontend Improvements
- ✅ Environment variable support for API URL
- ✅ Request/response logging for all API calls
- ✅ Better error handling in Google login flow
- ✅ Detailed console logs for troubleshooting

---

## 📋 Pre-Deployment Checklist

### 1. Google Cloud Console Setup
- [ ] Create OAuth 2.0 Client ID
- [ ] Enable Google+ API
- [ ] Add authorized JavaScript origins:
  ```
  https://yourdomain.vercel.app
  https://yourdomain.vercel.app:443
  ```
- [ ] Add authorized redirect URIs:
  ```
  https://yourdomain.vercel.app/
  https://yourdomain.vercel.app
  ```
- [ ] Copy Client ID and Client Secret

### 2. Database Preparation
- [ ] Create MySQL database (PlanetScale recommended)
- [ ] Run database migrations
- [ ] Note: DB_HOST, DB_PORT, DB_USER, DB_PASSWORD, DB_NAME

### 3. Local Testing
- [ ] Test Google Sign-In locally (http://localhost:5173)
- [ ] Check browser console for errors (F12)
- [ ] Verify network requests work

---

## 🚀 Step 1: Deploy Backend to Vercel

### Method A: Using Vercel Web Interface (Recommended)

```
1. Go to https://vercel.com/dashboard
2. Click "Add New..." → "Project"
3. Import GitHub repository
4. Configure:
   - Framework: Other (Node.js)
   - Root Directory: backend/
   - Build Command: Leave blank
   - Output Directory: Leave blank

5. Add Environment Variables:
   DB_HOST=your-db-host.mysql.database.azure.com
   DB_PORT=3306
   DB_USER=your-db-user
   DB_PASSWORD=your-db-password
   DB_NAME=bookmycampus
   GOOGLE_CLIENT_ID=your-client-id.apps.googleusercontent.com
   GOOGLE_CLIENT_SECRET=your-client-secret
   CORS_ORIGIN=https://yourdomain.vercel.app
   NODE_ENV=production
   PORT=5000

6. Click "Deploy"
```

### Method B: Using Vercel CLI

```bash
cd backend
vercel --prod --env DB_HOST=xxx --env DB_PORT=3306 ...
```

**Important:** Note your backend URL (e.g., `https://campus-api.vercel.app`)

---

## 🚀 Step 2: Deploy Frontend to Vercel

### Using Vercel Web Interface

```
1. Go to https://vercel.com/dashboard
2. Click "Add New..." → "Project"
3. Import same GitHub repository
4. Configure:
   - Framework: Vite (auto-detected)
   - Root Directory: frontend-react/
   - Build Command: npm run build
   - Output Directory: dist

5. Add Environment Variables:
   VITE_API_URL=https://your-backend-url.vercel.app
   VITE_GOOGLE_CLIENT_ID=your-client-id.apps.googleusercontent.com
   VITE_GEMINI_API_KEY=your-gemini-api-key

6. Click "Deploy"
```

**Note:** Use the backend URL from Step 1

---

## 🧪 Testing Deployment

### Test Google Sign-In

1. **Open your frontend URL in browser**
   ```
   https://yourdomain.vercel.app
   ```

2. **Click "Sign in with Google"**
   - You should see Google consent screen
   - Select your Google account
   - Should redirect to dashboard

3. **Open Browser Console (F12) and check for:**
   ```
   ✅ "🔐 Google login request received:"
   ✅ "👤 Creating new Google user:"
   ✅ "✅ Google login successful for:"
   ```

4. **Check Network Tab:**
   - Request: `POST /google-login`
   - Response: Status `200` with user data
   - No CORS errors

---

## 🐛 Troubleshooting

### Error: "Failed to authenticate with server"

**Cause:** Backend API URL is wrong or backend not deployed

**Solution:**
1. Check `VITE_API_URL` environment variable in Vercel
2. Verify backend is deployed and running
3. Test backend health: `https://your-backend.vercel.app/health`
4. Check browser console for the exact error

### Error: "403 Forbidden" on Google Sign-In

**Cause:** Redirect URI not configured in Google Cloud Console

**Solution:**
1. Go to Google Cloud Console
2. Add your Vercel domain to:
   - Authorized JavaScript Origins
   - Authorized Redirect URIs
3. Wait 5-10 minutes for changes to propagate
4. Clear browser cache and try again

### Error: "Database connection error"

**Cause:** Wrong database credentials or database is down

**Solution:**
1. Verify credentials in Vercel environment variables
2. Check database is accessible from Vercel (no IP restrictions)
3. Test connection locally first
4. Check PlanetScale/database provider dashboard

### CORS Error: "Access to XMLHttpRequest blocked"

**Cause:** Frontend domain not in backend CORS_ORIGIN

**Solution:**
1. Go to Vercel backend project settings
2. Update `CORS_ORIGIN` to include frontend URL:
   ```
   CORS_ORIGIN=https://frontend-domain.vercel.app,https://another-domain.vercel.app
   ```
3. Redeploy backend
4. Wait 1-2 minutes and retry

### Error: "Cannot POST /google-login"

**Cause:** Backend route not found or backend not responding

**Solution:**
1. Check backend is deployed to Vercel
2. Test: `curl -X POST https://your-backend.vercel.app/health`
3. Check backend logs in Vercel dashboard
4. Verify route is in `backend/routes/auth.js`

---

## 📊 Environment Variables Reference

### Backend (Vercel Settings)
| Variable | Example | Notes |
|----------|---------|-------|
| `DB_HOST` | `db.planetscale.com` | MySQL host |
| `DB_PORT` | `3306` | MySQL port |
| `DB_USER` | `your_user` | MySQL username |
| `DB_PASSWORD` | `xxx` | MySQL password |
| `DB_NAME` | `bookmycampus` | Database name |
| `GOOGLE_CLIENT_ID` | `xxx.apps.googleusercontent.com` | From Google Cloud |
| `GOOGLE_CLIENT_SECRET` | `xxx` | From Google Cloud |
| `CORS_ORIGIN` | `https://yourdomain.vercel.app` | Frontend URL |
| `NODE_ENV` | `production` | Fixed value |
| `PORT` | `5000` | Vercel uses any port |

### Frontend (.env.local in Vercel)
| Variable | Example | Notes |
|----------|---------|-------|
| `VITE_API_URL` | `https://your-backend.vercel.app` | Backend URL |
| `VITE_GOOGLE_CLIENT_ID` | `xxx.apps.googleusercontent.com` | Same as backend |
| `VITE_GEMINI_API_KEY` | `xxx` | For CampusMitra AI |

---

## 🔍 Debug Logs

To see detailed logs:

1. **Backend logs**: Go to Vercel Dashboard → Backend Project → "Deployments" → Click deployment → "Logs"

2. **Frontend logs**: Open browser console (F12) and look for:
   ```
   📡 API Base URL: ...
   📤 API Request: POST /google-login
   ✅ API Response: 200 - ...
   ```

3. **Network logs**: In browser DevTools, Network tab:
   - Click POST request to `/google-login`
   - Check "Response" tab for error details
   - Check "Headers" tab for CORS headers

---

## ✨ Success Indicators

You know it's working when:

- ✅ Homepage loads without errors
- ✅ Google Sign-In button is clickable
- ✅ Clicking button opens Google consent screen
- ✅ After signing in, redirects to dashboard
- ✅ User data is displayed in dashboard
- ✅ No errors in browser console (F12)
- ✅ Network tab shows `POST /google-login` with 200 status

---

## 🔗 Important URLs

| Service | Your URL |
|---------|----------|
| **Frontend** | `https://yourdomain.vercel.app` |
| **Backend API** | `https://your-backend.vercel.app` |
| **Backend Health** | `https://your-backend.vercel.app/health` |
| **Vercel Dashboard** | https://vercel.com/dashboard |
| **Google Cloud** | https://console.cloud.google.com |
| **GitHub** | https://github.com/yourusername/repo |

---

## 📞 Getting Help

If you still have issues:

1. **Check browser console (F12)** - Most errors are logged here
2. **Check Vercel logs** - Backend deployment logs
3. **Test API directly** - Use curl or Postman
   ```bash
   curl -X POST https://your-backend.vercel.app/google-login \
     -H "Content-Type: application/json" \
     -d '{"email":"test@example.com","name":"Test User"}'
   ```
4. **Verify environment variables** - Double-check each one
5. **Check Google Cloud Console** - Verify OAuth configuration

---

## 🎉 You're Done!

Once everything is working:
- Both frontend and backend deployed to Vercel
- Google OAuth 2.0 authentication working
- All features functional
- Ready for production use!

Good luck! 🚀
