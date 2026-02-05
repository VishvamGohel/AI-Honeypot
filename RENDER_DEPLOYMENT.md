# 🚀 Render.com Deployment Guide

## Step-by-Step Deployment

### 1. Prepare Your Project

Make sure these files are in your project root:
- ✅ `requirements.txt`
- ✅ `.gitignore`
- ✅ `render.yaml` (optional, but helpful)
- ✅ All your source code in `src/` folder

### 2. Push to GitHub

**If you don't have Git initialized:**

```bash
# Navigate to your project folder
cd path/to/your/honeypot-project

# Initialize git
git init

# Add all files
git add .

# Commit
git commit -m "Initial commit - Agentic Honeypot ready for deployment"

# Create a new repository on GitHub (github.com/new)
# Then link it:
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git

# Push
git push -u origin main
```

**If you already have it on GitHub:**
```bash
git add .
git commit -m "Ready for Render deployment"
git push
```

---

### 3. Deploy to Render.com

1. **Go to:** https://render.com
2. **Sign up/Login** (can use GitHub account)
3. Click **"New +"** → **"Web Service"**
4. **Connect your GitHub repository:**
   - Click "Connect account" if first time
   - Select your honeypot repository
   - Click "Connect"

5. **Configure the service:**
   ```
   Name: agentic-honeypot (or any name you want)
   Region: Choose closest to you (or Singapore for India)
   Branch: main
   Runtime: Python 3
   Build Command: pip install -r requirements.txt
   Start Command: uvicorn src.api.server:app --host 0.0.0.0 --port $PORT
   Plan: Free
   ```

6. **Add Environment Variables:**
   
   Click **"Advanced"** → **"Add Environment Variable"**
   
   Add these:
   ```
   Key: GROQ_API_KEY
   Value: [paste your actual Groq API key]
   
   Key: API_KEY
   Value: TEST_API_KEY
   
   Key: PYTHON_VERSION
   Value: 3.11.0
   ```

7. **Click "Create Web Service"**

---

### 4. Wait for Deployment

- ⏳ First deployment takes **5-10 minutes**
- 📊 Watch the logs in real-time
- ✅ Look for: `Application startup complete`
- 🎉 Status will change to **"Live"**

---

### 5. Get Your Public URL

Once deployed:
- Your URL will be: `https://agentic-honeypot.onrender.com` (or similar)
- Find it at the top of the Render dashboard
- Copy this URL

---

### 6. Test Your Deployment

**Test health endpoint:**
```bash
curl https://your-app-name.onrender.com/health
```

**Or in PowerShell:**
```powershell
Invoke-RestMethod -Uri "https://your-app-name.onrender.com/health"
```

Expected response:
```json
{
  "status": "ok",
  "sessions": 0,
  "groq_key_present": true
}
```

**Test the main endpoint:**
```powershell
Invoke-RestMethod `
  -Uri "https://your-app-name.onrender.com/honeypot/message" `
  -Method POST `
  -Headers @{
    "Content-Type" = "application/json"
    "x-api-key" = "TEST_API_KEY"
  } `
  -Body (ConvertTo-Json @{
    sessionId = "test-render-001"
    message = @{
      sender = "scammer"
      text = "Your account is blocked. Share OTP now."
      timestamp = "2026-01-21T10:15:30Z"
    }
    conversationHistory = @()
  }) | ConvertTo-Json -Depth 10
```

---

### 7. Update GUVI Submission

**Go back to GUVI and update:**
- ❌ OLD: `https://abc123.ngrok.io/honeypot/message`
- ✅ NEW: `https://your-app-name.onrender.com/honeypot/message`
- API Key: `TEST_API_KEY`

---

## 🎯 Important Notes

### Free Tier Limitations:
- ⚠️ **Spins down after 15 minutes of inactivity**
- ⏳ **Takes 30-60 seconds to wake up** on first request
- ✅ **This is usually fine for hackathons** - evaluators expect this

### Keeping It Warm (Optional):
If you want to prevent spin-down:
1. Use a service like **UptimeRobot** (free)
2. Ping your `/health` endpoint every 10 minutes
3. Or upgrade to paid plan ($7/month)

### Logs and Monitoring:
- View logs in Render dashboard
- See all requests and responses
- Check for errors in real-time

---

## 🆘 Troubleshooting

### Build Failed:
- Check `requirements.txt` syntax
- Make sure Python version is correct
- Check logs for specific error

### Application Error / 503:
- Check environment variables are set
- Verify `GROQ_API_KEY` is correct
- Check logs for startup errors

### Can't Connect to Service:
- Wait for deployment to complete (shows "Live")
- Check the URL is correct
- Try `/health` endpoint first

### groq_key_present: false:
- Go to Render dashboard
- Environment → Add `GROQ_API_KEY`
- Click "Save Changes"
- Service will auto-redeploy

---

## ✅ Deployment Checklist

- [ ] Code pushed to GitHub
- [ ] Render account created
- [ ] Web Service created and connected to repo
- [ ] Environment variables set (GROQ_API_KEY, API_KEY)
- [ ] Build command: `pip install -r requirements.txt`
- [ ] Start command: `uvicorn src.api.server:app --host 0.0.0.0 --port $PORT`
- [ ] Service shows "Live" status
- [ ] `/health` endpoint returns `{"status": "ok"}`
- [ ] Test message endpoint works
- [ ] GUVI submission updated with new URL
- [ ] You can close your laptop! ✅

---

## 🎉 You're Done!

Your honeypot is now:
- ✅ Running 24/7 on Render
- ✅ Accessible via permanent URL
- ✅ No need to keep your computer on
- ✅ Auto-restarts if it crashes
- ✅ Free tier is sufficient for hackathon

**Next:** Just monitor the Render logs during evaluation period to see the requests coming in!

---

## 💡 Alternative: Railway.app

If Render doesn't work, try Railway.app:
1. Go to railway.app
2. "New Project" → "Deploy from GitHub"
3. Select your repo
4. Add same environment variables
5. Railway auto-detects Python and deploys
6. Get URL from dashboard

Both are equally good for hackathons!
