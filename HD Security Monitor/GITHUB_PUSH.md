# Push to GitHub - Step by Step

**Developed by GumbiTech**

This guide shows exactly how to upload your project to GitHub.

## Prerequisites

1. **GitHub Account** - Create one at [github.com](https://github.com)
2. **Git Installed** - Download from [git-scm.com](https://git-scm.com)
3. **Personal Access Token** - For authentication

---

## Step 1: Create GitHub Account

1. Go to https://github.com
2. Click "Sign Up"
3. Follow the registration steps
4. Verify your email

---

## Step 2: Create a New Repository

1. Login to GitHub
2. Click "+" → "New repository"
3. Fill in:
   - **Repository name:** `hd-security-monitor`
   - **Description:** `Professional USB webcam security monitoring with multiple vision filters`
   - **Public/Private:** Choose (Public = anyone can see, Private = only you)
   - **Initialize:** Check "Add a README.md"
4. Click "Create repository"

---

## Step 3: Install Git

Download and install from https://git-scm.com/download/win

**Verify installation:**
```bash
git --version
```

---

## Step 4: Configure Git (First Time Only)

Open PowerShell and run:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

**Verify:**
```bash
git config --global --list
```

---

## Step 5: Clone Your Repository

Navigate to where you want the project:

```bash
cd C:\Users\YourName\Desktop\My Python Files
```

Clone (copy) your repository from GitHub:

```bash
git clone https://github.com/YOUR_USERNAME/hd-security-monitor.git
```

This creates a folder with your repository.

---

## Step 6: Add Your Files to the Local Repository

Copy your project files into the cloned folder:
- `hd_cam.py` (main script)
- `requirements.txt`
- `README.md`
- `SETUP.md`
- `FEATURES.md`
- `CONTROLS.md`
- `CODE_EXPLANATION.md`
- `.gitignore`
- `LICENSE.md`

Navigate into the repository:

```bash
cd hd-security-monitor
```

---

## Step 7: Stage Files for Commit

Tell Git which files to upload:

**Add all files:**
```bash
git add .
```

**Verify what will be uploaded:**
```bash
git status
```

You should see all your files listed (green = staged).

---

## Step 8: Create a Commit

Commit = a snapshot of your code with a message:

```bash
git commit -m "Initial commit: HD Security Monitor with multi-mode video processing"
```

**Good commit messages explain what changed:**
```bash
git commit -m "Add NVG night vision mode"
git commit -m "Fix camera resolution fallback"
git commit -m "Update documentation"
```

---

## Step 9: Create Personal Access Token (Authentication)

GitHub no longer accepts passwords for git commands. You need a token:

1. Go to GitHub → Settings → Developer settings → Personal access tokens
2. Click "Generate new token"
3. Name it: `my-git-token`
4. Checkboxes to enable: `repo` and `workflow`
5. Click "Generate token"
6. **COPY THE TOKEN** (won't show again)
7. Save it somewhere safe

---

## Step 10: Push to GitHub

Upload your code to GitHub:

```bash
git push origin main
```

**First time, you'll be asked:**
```
Username for 'https://github.com': YOUR_USERNAME
Password for 'https://github.com': [PASTE YOUR TOKEN HERE]
```

**Success** means you see:
```
Counting objects: 100% (8/8)
Delta compression using...
Writing objects: 100% (8/8)
remote: Resolving deltas: 100% (2/2)
To https://github.com/YOUR_USERNAME/hd-security-monitor.git
   abc1234..def5678  main -> main
```

---

## Verify on GitHub

1. Go to https://github.com/YOUR_USERNAME/hd-security-monitor
2. You should see all your files listed
3. README.md should display automatically

---

## File Structure on GitHub

Your repository should have:

```
hd-security-monitor/
├── hd_cam.py                    # Main application
├── requirements.txt              # Python dependencies
├── README.md                     # Project overview
├── SETUP.md                      # Installation guide
├── FEATURES.md                   # Feature explanations
├── CONTROLS.md                   # Keyboard controls
├── CODE_EXPLANATION.md           # Code breakdown
├── LICENSE.md                    # License
└── .gitignore                    # What to ignore
```

---

## Updating Your Repository (Later)

After making changes locally:

```bash
# Stage changes
git add .

# Commit with message
git commit -m "Your message here"

# Push to GitHub
git push origin main
```

---

## Useful Git Commands

| Command | Purpose |
|---------|---------|
| `git status` | See what's changed |
| `git add .` | Stage all files |
| `git commit -m "msg"` | Create commit |
| `git push origin main` | Upload to GitHub |
| `git pull origin main` | Download from GitHub |
| `git log` | View commit history |
| `git diff` | See what changed |

---

## Sharing Your Repository

Once on GitHub, share the link:

**URL:** `https://github.com/YOUR_USERNAME/hd-security-monitor`

**Anyone can:**
- View your code
- Download it
- Fork it (make their own copy)
- Create issues (suggest features)
- Submit pull requests (propose changes)

---

## Troubleshooting

### "fatal: not a git repository"
**Solution:** Make sure you're in the right folder
```bash
cd path/to/hd-security-monitor
```

### "Permission denied (publickey)"
**Solution:** Check Git configuration
```bash
git config --global user.email
git config --global user.name
```

### "remote: Repository not found"
**Solution:** Check the URL
```bash
git remote -v
# Should show your GitHub URL
```

### Token Not Working
**Solution:** Generate a new token at GitHub → Settings → Developer settings → Personal access tokens

---

## After Uploading

1. **Add a profile README** (make your GitHub profile look professional)
2. **Star the project yourself** (encourages others)
3. **Add topics** (GitHub → About → Topics → Add `python`, `opencv`, `security`)
4. **Enable discussions** (for user feedback)
5. **Consider adding issues labels** (bug, feature request, documentation)

---

## Example: Your GitHub Page

After pushing, your repository will look like:

```
📁 hd-security-monitor

⭐ Stars: 25
🔀 Forks: 5
👁️ Watchers: 10

Professional USB webcam security monitoring with multiple vision filters

📖 README
📦 Code
🔖 Tags
```

---

## Making Your Project Shareable

Add to your social media:

✅ Share on Twitter/X:
> "Just uploaded my security monitoring project to GitHub! Features motion detection, night vision mode, and multi-webcam support. Check it out! 🎥 [link]"

✅ Add to portfolio/resume:
> "HD Security Monitor - Python OpenCV application for USB webcam monitoring with 5 vision filters"

✅ Show off to friends/colleagues

---

## Next Steps

1. ✅ Upload to GitHub
2. ✅ Configure README (GitHub recognizes Markdown automatically)
3. ✅ Add GitHub link to your portfolio
4. ✅ Consider adding more features and committing updates
5. ✅ Help others in Github community

---

**Your project is now on GitHub! 🎉**

Anyone can now:
- Learn from your code
- Use your application
- Contribute improvements
- Reference your work

Good luck! 🚀
