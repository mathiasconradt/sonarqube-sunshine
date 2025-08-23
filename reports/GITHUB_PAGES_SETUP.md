# 🌐 GitHub Pages Setup for Live HTML Reports

Your workflow has been updated to automatically deploy reports to GitHub Pages! This means you can view your HTML reports directly in your browser without downloading anything.

## 🚀 One-Time Setup

### Step 1: Enable GitHub Pages
1. Go to your repository on GitHub
2. Click **Settings** tab
3. Scroll down to **Pages** section (left sidebar)
4. Under **Source**, select **Deploy from a branch**
5. Choose branch: **gh-pages**
6. Choose folder: **/ (root)**
7. Click **Save**

### Step 2: Set Proper Permissions
1. In the same repository, go to **Settings** → **Actions** → **General**
2. Scroll down to **Workflow permissions**
3. Select **Read and write permissions**
4. Check **Allow GitHub Actions to create and approve pull requests**
5. Click **Save**

## 🎯 How It Works

### After Setup:
1. **Push to main/master** → Workflow runs automatically
2. **Report generates** → Uploaded as artifact (zip download)
3. **GitHub Pages deploys** → Live website created!
4. **Job summary shows link** → Click to view instantly

### Your Report URLs:
```
Main Report: https://YOUR_USERNAME.github.io/YOUR_REPO_NAME/reports/report.html
```

Replace:
- `YOUR_USERNAME` with your GitHub username/org name
- `YOUR_REPO_NAME` with your repository name

## ✨ Benefits

### 🌐 **Live Website**
- No downloads needed
- Direct browser viewing
- Bookmark-able URL
- Mobile-friendly

### 🔄 **Auto-Updates**
- New reports automatically replace old ones
- Always shows the latest security status
- Historical reports preserved if needed

### 🔗 **Easy Sharing**
- Send direct links to stakeholders
- Embed in documentation
- Reference in tickets/emails

## 🛠️ Customization Options

### Change Report Path
```yaml
destination_dir: my-custom-path  # Changes URL to /my-custom-path/report.html
```

### Keep Historical Reports
```yaml
destination_dir: reports/${{ github.run_number }}  # Each run gets unique folder
```

### Custom Domain (Advanced)
1. Add `CNAME` file to your repo with your domain
2. Configure DNS settings
3. Enable custom domain in Pages settings

## 📊 Workflow Behavior

### **On Main/Master Push:**
- ✅ Generates report
- ✅ Uploads artifact (zip download)
- ✅ Deploys to GitHub Pages (live website)
- ✅ Shows clickable link in job summary

### **On Other Branches:**
- ✅ Generates report
- ✅ Uploads artifact (zip download)
- ❌ No Pages deployment (keeps main report clean)

### **Manual Runs:**
- Same as branch-based behavior above

## 🔍 Troubleshooting

### "Pages not found (404)"
- Ensure Pages is enabled with `gh-pages` branch
- Wait 5-10 minutes after first deployment
- Check workflow logs for deployment errors

### "Permission denied"
- Enable "Read and write permissions" in Actions settings
- Make sure `GITHUB_TOKEN` has necessary permissions

### "Report not updating"
- Clear browser cache
- Check if workflow completed successfully
- Verify you pushed to main/master branch

### "Workflow fails on Pages step"
- Ensure repository is public (or has GitHub Pro/Enterprise for private Pages)
- Check Actions permissions are set correctly

## 📱 Mobile Access

Your reports work perfectly on mobile devices:
- Responsive design
- Touch-friendly navigation  
- Share links via text/email
- Access from anywhere

## 🎉 Result

After setup, every time you push to main/master:

1. **Workflow runs** ⚡
2. **Artifact created** 📁 (for backup/download)
3. **Live report published** 🌐 (for instant viewing)
4. **Link appears in job summary** 🔗 (one-click access)

No more downloading zip files - just click and view your security reports instantly! 🚀
