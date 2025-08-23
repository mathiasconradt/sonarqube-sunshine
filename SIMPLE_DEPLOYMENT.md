# 🚀 Simple Deployment to Any Repository

## Quick Copy-Paste Solution

If you want the **simplest possible** way to add SonarQube Sunshine to any repository, just copy-paste this workflow:

### 📁 Create: `.github/workflows/security-report.yml`

```yaml
name: Security Report

on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

jobs:
  security-report:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'

    - name: Download SonarQube Sunshine
      run: |
        curl -sL https://raw.githubusercontent.com/mathiasconradt/sonarqube-sunshine/main/sonarqube-sunshine.py -o sonarqube-sunshine.py
        curl -sL https://raw.githubusercontent.com/mathiasconradt/sonarqube-sunshine/main/requirements.txt -o requirements.txt

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Generate Report
      run: |
        # Try SonarQube integration first, fallback to file input
        if [ -n "${{ secrets.SONAR_TOKEN }}" ]; then
          python sonarqube-sunshine.py \
            -t "${{ secrets.SONAR_TOKEN }}" \
            -u "${{ secrets.SONAR_HOST_URL }}" \
            -c "${{ secrets.COMPONENT_KEY }}" \
            -b "${{ github.ref_name }}" \
            -o report.html \
            -e
        else
          # Look for SBOM files
          if [ -f "sbom.json" ]; then
            python sonarqube-sunshine.py -i sbom.json -o report.html -e
          elif [ -f "bom.json" ]; then
            python sonarqube-sunshine.py -i bom.json -o report.html -e
          elif [ -f "cyclone-x.json" ]; then
            python sonarqube-sunshine.py -i cyclone-x.json -o report.html -e
          else
            echo "No SBOM file found and no SonarQube secrets configured"
            exit 1
          fi
        fi

    - name: Upload Report as Artifact
      uses: actions/upload-artifact@v4
      with:
        name: security-report
        path: report.html

    - name: Deploy to GitHub Pages
      if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/master'
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: .
        destination_dir: reports
        keep_files: false
        publish_branch: gh-pages
        user_name: 'github-actions[bot]'
        user_email: 'github-actions[bot]@users.noreply.github.com'
        commit_message: 'Deploy Security Report'

    - name: Show Results
      if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/master'
      run: |
        echo "🎉 Security report generated!"
        echo "📊 Download: Check Artifacts above"
        echo "🌐 Online: https://${{ github.repository_owner }}.github.io/${{ github.event.repository.name }}/reports/report.html"
```

## 🔧 Setup (3 Steps)

### 1. Add Secrets (for SonarQube integration)
Go to your repository **Settings → Secrets → Actions**:
- `SONAR_TOKEN`
- `SONAR_HOST_URL` 
- `COMPONENT_KEY`

### 2. Enable GitHub Pages
Go to **Settings → Pages**:
- Source: **Deploy from a branch**
- Branch: **gh-pages**

### 3. Set Permissions
Go to **Settings → Actions → General**:
- Workflow permissions: **Read and write permissions**

## ✨ That's It!

Push the workflow file and it will:
- ✅ Auto-download the latest SonarQube Sunshine script
- ✅ Generate security reports
- ✅ Create downloadable artifacts  
- ✅ Deploy to GitHub Pages
- ✅ Show the live URL in logs

**Works in any repository immediately!** 🚀
