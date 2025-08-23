# 🌞 SonarQube Sunshine

**Transform your SonarQube SCA vulnerability data into beautiful, interactive security reports.**

SonarQube Sunshine generates rich HTML reports from SonarQube data or CycloneDX SBOM files, featuring interactive charts, vulnerability analysis, and EPSS/CISA KEV enrichment.

[![Example Report](https://img.shields.io/badge/🌐_Live_Demo-View_Report-blue?style=for-the-badge)](https://conradt.net/sonarqube-sunshine/reports/report.html)

> **⚠️ Experimental Project**  
> This project is based on [CycloneDX Sunshine](https://github.com/CycloneDX/Sunshine) and is purely experimental. It's designed for testing and evaluation purposes. Use in production environments at your own discretion.

## ✨ Features

- 🔍 **Interactive vulnerability charts** with dependency visualization
- 📊 **Component analysis** with risk assessment
- 🛡️ **EPSS scores** for vulnerability prioritization  
- 🎯 **CISA KEV integration** for critical vulnerabilities
- 🌐 **GitHub Pages deployment** for easy sharing
- 🔄 **Automated reports** via GitHub Actions
- 📱 **Mobile-friendly** responsive design

---

## 🚀 Quick Start

### Option 1: GitHub Actions (Recommended)

Add this to your repository at `.github/workflows/security-report.yml`:

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

    - name: Generate Security Report
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

**That's it!** Your reports will be automatically generated and published to GitHub Pages.

### Option 2: Local Usage

```bash
# Install dependencies
pip install -r requirements.txt

# Generate report from SonarQube
python sonarqube-sunshine.py \
  -t YOUR_SONAR_TOKEN \
  -u https://your-sonarqube-instance.com \
  -c your-project-key \
  -o report.html \
  -e

# Or from SBOM file
python sonarqube-sunshine.py \
  -i your-sbom.json \
  -o report.html \
  -e
```

---

## ⚙️ GitHub Actions Setup

### 📋 Prerequisites

⚠️ **You need either SonarQube secrets OR an SBOM file in your repository**

### 🔐 Required Secrets

For SonarQube integration, add these secrets to your repository (**Settings → Secrets → Actions**):

- `SONAR_TOKEN` - Your SonarQube authentication token
- `SONAR_HOST_URL` - Your SonarQube instance URL (e.g., `https://next.sonarqube.com/sonarqube`)
- `COMPONENT_KEY` - Your project key in SonarQube

### 🌐 GitHub Pages Setup (Optional)

To publish reports as live websites:

1. **Enable GitHub Pages:**
   - Go to **Settings → Pages**
   - Source: **Deploy from a branch**  
   - Branch: **gh-pages**
   - Folder: **/ (root)**
   - Click **Save**

2. **Set Workflow Permissions:**
   - Go to **Settings → Actions → General**
   - Workflow permissions: **Read and write permissions**
   - Check **Allow GitHub Actions to create and approve pull requests**
   - Click **Save**

### 🎛️ Action Inputs

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

**Note:** Branch name is automatically detected from the GitHub Actions pipeline context.

---

## 📚 Usage Examples

### Java Maven Project with SonarQube

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

### Container Project with SBOM

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
    
    # Generate SBOM using Syft
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

### File-Based Input Only

```yaml
- name: Generate Report from SBOM
  uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
  with:
    input-file: 'path/to/your/sbom.json'
    output-file: 'security-report.html'
    enrich-cves: true
    deploy-to-pages: false  # Artifact only
```

### Multi-Project Repository

```yaml
jobs:
  frontend-report:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
      with:
        component-key: 'frontend-component'
        output-file: 'frontend-report.html'

  backend-report:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: mathiasconradt/sonarqube-sunshine/.github/actions/sonarqube-sunshine@main
      with:
        component-key: 'backend-component'
        output-file: 'backend-report.html'
```

---

## 🔧 Command Line Usage

### Installation

```bash
pip install -r requirements.txt
```

### Basic Commands

```bash
# SonarQube integration with CVE enrichment
python sonarqube-sunshine.py \
  -t YOUR_SONAR_TOKEN \
  -u https://next.sonarqube.com/sonarqube \
  -c your-project-key \
  -b main \
  -o report.html \
  -e

# File input mode
python sonarqube-sunshine.py \
  -i sbom.json \
  -o report.html \
  -e

# Auto-detect SBOM files
python sonarqube-sunshine.py \
  -o report.html \
  -e
```

### Parameters

| Short | Long | Description |
|-------|------|-------------|
| `-t` | `--sonar-token` | SonarQube authentication token |
| `-u` | `--sonar-host-url` | SonarQube host URL |
| `-c` | `--component-key` | SonarQube component key |
| `-b` | `--branch` | Branch name for SonarQube reports |
| `-i` | `--input` | Path of input CycloneDX file |
| `-o` | `--output` | Path of output HTML file |
| `-e` | `--enrich` | Enrich CVEs with EPSS and CISA KEV |
| `-v` | `--version` | Show program version |

---

## 🌐 How GitHub Pages Works

### Automatic Deployment

When you push to `main` or `master`:

1. **Workflow runs** ⚡ and generates your security report
2. **GitHub Pages deploys** 🚀 the report to a live website  
3. **Job summary shows** 🔗 the direct URL to view your report
4. **No downloads needed** - just click and view!

### Your Report URLs

```
https://YOUR_USERNAME.github.io/YOUR_REPO_NAME/reports/report.html
```

Replace `YOUR_USERNAME` and `YOUR_REPO_NAME` with your GitHub details.

### Benefits

- ✅ **No downloads needed** - direct browser viewing
- ✅ **Auto-updates** - new reports replace old ones automatically  
- ✅ **Shareable URLs** - send direct links to teammates
- ✅ **Mobile-friendly** - works on phones/tablets
- ✅ **Bookmark-able** - save the URL for quick access
- ✅ **Still keeps artifacts** - for backup if needed

### Workflow Behavior

**On Main/Master Push:**
- ✅ Generates report
- ✅ Uploads artifact (zip download)
- ✅ Deploys to GitHub Pages (live website)
- ✅ Shows clickable link in job summary

**On Other Branches:**
- ✅ Generates report
- ✅ Uploads artifact (zip download)
- ❌ No Pages deployment (keeps main report clean)

---

## 🔍 Troubleshooting

### Common Issues

**"No SBOM file found"**
- Ensure your repository contains: `sbom.json`, `bom.json`, `cyclone-x.json`, or `sbom.cdx.json`
- Or provide the file path in the `input-file` parameter

**"SonarQube authentication failed"**
- Check your `SONAR_TOKEN` secret is valid and not expired
- Verify `SONAR_HOST_URL` is correct (include `https://`)
- Confirm `COMPONENT_KEY` matches your project in SonarQube

**"Pages not found (404)"**
- Ensure GitHub Pages is enabled with `gh-pages` branch
- Wait 5-10 minutes after first deployment
- Check workflow logs for deployment errors

**"Permission denied" during deployment**
- Enable "Read and write permissions" in Actions settings
- Make sure `GITHUB_TOKEN` has necessary permissions

**"Script download failed"**
- Check if this repository is public and accessible
- Verify network connectivity in GitHub Actions

### Debug Mode

Add this step to your workflow for verbose logging:

```yaml
- name: Debug Information
  run: |
    echo "Python version: $(python --version)"
    echo "Current directory: $(pwd)"
    echo "Files in directory: $(ls -la)"
    echo "Environment variables:"
    env | grep -E "(SONAR|GITHUB)" | sort
```

---

## 🎯 Report Features

### Interactive Charts
- **Dependency visualization** with hierarchical relationships
- **Vulnerability severity** color coding
- **Click to focus** on specific components
- **Hover for details** on components and vulnerabilities

### Risk Assessment
- **EPSS scores** for vulnerability prioritization
- **CISA KEV flags** for critical vulnerabilities
- **Severity distribution** analysis
- **Component risk** evaluation

### Data Sources
- **SonarQube** - Direct API integration
- **CycloneDX SBOM** - Standard format support
- **EPSS API** - Real-time exploit prediction scores
- **CISA KEV** - Known Exploited Vulnerabilities catalog

---

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **Created by** [Luca Capacci](https://github.com/lucacapacci)
- **Contributor** [Mattia Fierro](https://github.com/mattiafierro)
- **Enhanced by** [Mathias Conradt](https://github.com/mathiasconradt)

---

## 📞 Support

- 🐛 **Issues**: [GitHub Issues](https://github.com/mathiasconradt/sonarqube-sunshine/issues)
- 💡 **Feature Requests**: [GitHub Discussions](https://github.com/mathiasconradt/sonarqube-sunshine/discussions)
- 📧 **Contact**: Open an issue for questions

---

**🌞 Make your security data shine!** Transform complex vulnerability data into actionable insights with beautiful, interactive reports.
