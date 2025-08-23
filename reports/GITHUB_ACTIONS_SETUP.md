# GitHub Actions Setup for SonarQube Sunshine

This guide explains how to set up GitHub Actions to automatically generate SonarQube Sunshine reports and save them as artifacts.

## 🎯 What You Get

- Automatic report generation on push/PR
- HTML reports saved as downloadable artifacts
- Support for both SonarQube integration and file-based input
- PR comments with download links
- Configurable CVE enrichment

## 🚀 Quick Setup

### Option 1: SonarQube Integration (Recommended)

1. **Add Repository Secrets** (Settings → Secrets and variables → Actions):
   ```
   SONAR_TOKEN=your_sonarqube_token
   SONAR_HOST_URL=https://your-sonarqube-instance.com
   COMPONENT_KEY=your-project-key
   BRANCH=main  # Optional - defaults to current branch if not set
   ```

2. **Commit the workflow file** - Choose one:
   - `sonarqube-sunshine.yml` - Full-featured with manual triggers
   - `simple-sunshine-report.yml` - Simplified version

3. **Push to main/master branch** - The workflow will run automatically!

### Option 2: File-Based Input

1. **Add your SBOM file** to your repository (e.g., `sbom.json`)

2. **Commit the workflow file**

3. **The workflow will automatically detect and process your SBOM file**

## 📋 Workflow Features

### Full Workflow (`sonarqube-sunshine.yml`)

- ✅ **Automatic triggers**: Push, PR, manual
- ✅ **Manual parameters**: Configure input file, SonarQube settings, enrichment
- ✅ **Smart detection**: Automatically finds SBOM files
- ✅ **PR integration**: Comments with download links
- ✅ **Job summaries**: Rich workflow results

### Simple Workflow (`simple-sunshine-report.yml`)

- ✅ **Basic triggers**: Push, manual
- ✅ **Auto-configuration**: Uses secrets or falls back to files
- ✅ **CVE enrichment**: Always enabled

## 🔧 Manual Workflow Triggers

For the full workflow, you can trigger it manually with custom parameters:

1. Go to **Actions** → **SonarQube Sunshine Report** → **Run workflow**
2. Configure parameters:
   - **Input file**: Path to CycloneDX file (optional)
   - **SonarQube host URL**: Override default URL
   - **Component key**: Override default component key
   - **Branch**: Specify branch name
   - **Enrich CVEs**: Enable EPSS and CISA KEV enrichment

## 📥 Downloading Reports

### From Workflow Run
1. Go to **Actions** tab
2. Click on your workflow run
3. Scroll down to **Artifacts** section
4. Download `sonarqube-sunshine-report` or `security-report`

### From PR Comments
- For PRs, the workflow automatically posts a comment with download instructions

## 🛠️ Customization

### Modify Report Name
```yaml
- name: Upload HTML Report
  uses: actions/upload-artifact@v4
  with:
    name: my-custom-report-name  # Change this
    path: report.html
```

### Add Multiple SBOM Files
```yaml
- name: Generate Multiple Reports
  run: |
    for file in *.json; do
      if [[ $file == *"sbom"* ]] || [[ $file == *"bom"* ]]; then
        python sonarqube-sunshine.py -i "$file" -o "report-${file%.json}.html" -e
      fi
    done

- name: Upload All Reports
  uses: actions/upload-artifact@v4
  with:
    name: all-security-reports
    path: report-*.html
```

### Schedule Regular Reports
```yaml
on:
  schedule:
    - cron: '0 2 * * 1'  # Every Monday at 2 AM
  push:
    branches: [ main, master ]
```

## 🔍 Troubleshooting

### Common Issues

**"No SBOM file found"**
- Ensure your repository contains a file named: `sbom.json`, `bom.json`, `cyclone-dx.json`, or `sbom.cdx.json`
- Or provide the file path manually in workflow parameters

**"SonarQube authentication failed"**
- Check your `SONAR_TOKEN` secret is valid
- Verify `SONAR_HOST_URL` is correct (include https://)
- Confirm `COMPONENT_KEY` matches your project

**"Branch not found in SonarQube"**
- Add `BRANCH` secret if you need a specific branch (optional)
- Without `BRANCH` secret, it uses the current Git branch automatically
- For manual runs, you can specify the branch in workflow parameters

**"Python script fails"**
- Check the Actions logs for detailed error messages
- Ensure your input file is valid CycloneDX format

### Debug Mode
Add this step to enable verbose logging:
```yaml
- name: Debug Information
  run: |
    echo "Python version: $(python --version)"
    echo "Current directory: $(pwd)"
    echo "Files in directory: $(ls -la)"
    echo "Environment variables:"
    env | grep -E "(SONAR|GITHUB)" | sort
```

## 📚 Examples

### Basic SonarQube Integration
```bash
# This happens automatically when you set up secrets
python sonarqube-sunshine.py \
  -t "$SONAR_TOKEN" \
  -u "$SONAR_HOST_URL" \
  -c "$COMPONENT_KEY" \
  -b "$BRANCH" \
  -o report.html \
  -e
```

### File-Based Processing
```bash
# Process local SBOM file
python sonarqube-sunshine.py \
  -i sbom.json \
  -o report.html \
  -e
```

### Manual Branch Specification
```bash
# For specific branch analysis
python sonarqube-sunshine.py \
  -t "$SONAR_TOKEN" \
  -u "$SONAR_HOST_URL" \
  -c "$COMPONENT_KEY" \
  -b "feature/my-branch" \
  -o report.html
```

---

🎉 **That's it!** Your GitHub Actions workflow is now set up to automatically generate security reports with every push or PR.
