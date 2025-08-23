# 🚀 Using SonarQube Sunshine in Any Repository

This guide shows you how to use SonarQube Sunshine in **any GitHub repository**, not just this one!

## 🎯 Quick Start for Other Repositories

### Option 1: Simple Copy-Paste (Easy)

**Copy this workflow file to your repository:**

Create `.github/workflows/security-report.yml` in your target repository:

```yaml
name: Generate Security Report

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

    - name: Generate SonarQube Sunshine Report
      uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
      with:
        sonar-token: ${{ secrets.SONAR_TOKEN }}
        sonar-host-url: ${{ secrets.SONAR_HOST_URL }}
        component-key: ${{ secrets.COMPONENT_KEY }}
        enrich-cves: true
        deploy-to-pages: true

    - name: Upload Report as Artifact
      uses: actions/upload-artifact@v4
      with:
        name: security-report
        path: report.html
```

### Option 2: Reusable Action (Recommended)

The action automatically:
- ✅ Downloads the latest `sonarqube-sunshine.py` script
- ✅ Installs Python dependencies
- ✅ Handles SonarQube integration or file input
- ✅ Deploys to GitHub Pages
- ✅ Creates downloadable artifacts

## 🔧 Setup Instructions

⚠️ **IMPORTANT: You need either SonarQube secrets OR an SBOM file in your repository**

### For SonarQube Integration:

1. **Add secrets to your target repository:**
   - `SONAR_TOKEN`
   - `SONAR_HOST_URL`
   - `COMPONENT_KEY`

2. **Enable GitHub Pages:**
   - Settings → Pages
   - Source: Deploy from branch
   - Branch: gh-pages

3. **Set Actions permissions:**
   - Settings → Actions → General
   - Workflow permissions: Read and write permissions

### For File-Based Input:

1. **Add your SBOM file to the repository** (e.g., `sbom.json`)
2. **Use the file input mode:**

```yaml
- name: Generate Report from SBOM
  uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
  with:
    input-file: 'path/to/your/sbom.json'
    enrich-cves: true
    deploy-to-pages: true
```

## 🎛️ Action Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `sonar-token` | SonarQube authentication token | No | from secrets |
| `sonar-host-url` | SonarQube host URL | No | from secrets |
| `component-key` | SonarQube component key | No | from secrets |
| `input-file` | Path to CycloneDX input file | No | auto-detect |
| `output-file` | Output HTML file name | No | `report.html` |
| `enrich-cves` | Enrich CVEs with EPSS and CISA KEV | No | `true` |
| `deploy-to-pages` | Deploy report to GitHub Pages | No | `true` |
| `pages-url` | Custom Pages URL | No | auto-detect |

**Note:** Branch name is automatically detected from the GitHub Actions pipeline context (`github.ref_name`).

## 📋 Complete Example Use Cases

### Use Case 1: Java Maven Project with SonarQube

```yaml
name: Security Report

on:
  push:
    branches: [ main ]

jobs:
  security-report:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Generate Security Report
      uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
      with:
        sonar-token: ${{ secrets.SONAR_TOKEN }}
        sonar-host-url: ${{ secrets.SONAR_HOST_URL }}
        component-key: 'my-java-project'
        enrich-cves: true
        deploy-to-pages: true
```

### Use Case 2: Container Project with Pre-generated SBOM

```yaml
name: Container Security Report

on:
  push:
    branches: [ main ]

jobs:
  security-report:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    # Generate SBOM using Syft or similar tool
    - name: Generate SBOM
      run: |
        docker run --rm -v $(pwd):/app anchore/syft:latest /app -o cyclonedx-json=/app/sbom.json
    
    - name: Generate Security Report
      uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
      with:
        input-file: 'sbom.json'
        output-file: 'container-security-report.html'
        enrich-cves: true
        deploy-to-pages: true
```

### Use Case 3: Multi-Project Mono-repo

```yaml
name: Multi-Project Security Reports

on:
  push:
    branches: [ main ]

jobs:
  frontend-report:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
      with:
        component-key: 'frontend-component'
        output-file: 'frontend-report.html'
        pages-url: 'https://company.github.io/project/frontend-report.html'

  backend-report:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
      with:
        component-key: 'backend-component'
        output-file: 'backend-report.html'
        pages-url: 'https://company.github.io/project/backend-report.html'
```

## 🌐 How It Works

1. **Action downloads the script:** `curl` fetches the latest `sonarqube-sunshine.py` from this repository
2. **Python setup:** Installs Python 3.9 and dependencies
3. **Smart execution:** Detects SonarQube vs file mode automatically
4. **Report generation:** Creates interactive HTML report
5. **GitHub Pages deployment:** Publishes to your repository's Pages site
6. **Artifact creation:** Saves report for manual download

## 🔄 Keeping Updated

The action uses `@main`, so you automatically get:
- ✅ Latest bug fixes
- ✅ New features
- ✅ Security updates

For stability, you can pin to a specific version:
```yaml
uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@v1.0.0
```

## 🐛 Troubleshooting

### Common Issues:

**"Script download failed"**
- Check if this repository is public
- Verify network connectivity in GitHub Actions

**"No SBOM file found"**
- Add `input-file` parameter with correct path
- Or configure SonarQube secrets

**"Pages deployment failed"**
- Enable GitHub Pages in target repository
- Set correct Actions permissions

**"SonarQube connection failed"**
- Verify SonarQube secrets are set correctly
- Check component key matches your project

## 🎉 Result

After setup, **any repository** can generate beautiful security reports with just:

1. **Copy workflow file** → Add to `.github/workflows/`
2. **Set secrets** → Add SonarQube credentials (if using)
3. **Push to main** → Report automatically generated!

Your report will be available at:
`https://YOUR_USERNAME.github.io/YOUR_REPO_NAME/reports/report.html`

**No need to copy Python scripts or maintain dependencies in each repository!** 🚀

## 📚 More Examples

Check the `reusable-action-examples.yml` file in this repository for additional workflow patterns and advanced configurations.

---

**Questions?** Open an issue in the [sonarqube-sunshine](https://github.com/mathiasconradt/sonarqube-sunshine) repository!
