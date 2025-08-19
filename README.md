# 🚀 Project Name: S3 Bucket-enum Nuclei-Template
![Maintenance](https://img.shields.io/badge/Maintained-Yes-green.svg)
<img src="https://img.shields.io/badge/contributions-welcome-magenta.svg?style=flat"></a>

### 📌 Overview

A comprehensive Nuclei template for discovering and assessing the security posture of AWS S3 buckets. This scanner goes beyond basic public access detection to provide thorough security analysis including write permissions, sensitive file exposure, and configuration vulnerabilities.

### 🚀 Features

### Core Security Testing
- **Multi-Region Scanning** - Tests buckets across 7+ major AWS regions
- **Public Access Detection** - Identifies publicly readable buckets with detailed access levels
- **Directory Listing Analysis** - Differentiates between listing-enabled vs listing-disabled public buckets
- **S3 Website Hosting Detection** - Identifies buckets configured for static website hosting
- **Security Headers Analysis** - Reviews encryption, CORS, and regional configuration headers

### Advanced Discovery
- **Smart Bucket Enumeration** - Tests 15+ common naming patterns including:
  - Environment-based: `prod-`, `dev-`, `test-`
  - Function-based: `-backup`, `-logs`, `-assets`, `-uploads`, `-static`, `-media`, `-data`
  - Subdomain variants: `www-`, company variations
- **Regional Endpoint Testing** - Comprehensive coverage of AWS regional endpoints
- **Alternative Access Methods** - Tests both virtual-hosted-style and path-style URLs

### Content Analysis
- **Sensitive File Detection** - Scans for critical files including:
  - Environment files (`.env`)
  - Configuration files (`config.json`)
  - Credentials and keys (`.pem`, `.key`, `credentials`)
  - Database backups (`.sql`, `.db`, `dump`)
  - Private files (`secret`, `private`, `backup`)
- **Metadata Extraction** - Captures bucket owner information, creation dates, and file listings

### Security Assessment
- **CVSS 3.1 Scoring** - Standardized vulnerability scoring (Current: 8.2 High)
- **CWE Mapping** - Maps findings to Common Weakness Enumeration
- **Risk Classification** - 5-tier risk matrix from INFO to CRITICAL
- **Header Analysis** - Reviews security headers for encryption status and CORS configuration

### Compliance & Reporting
- **Detailed Risk Matrix** - Clear severity levels for each finding type
- **Remediation Guidance** - Actionable steps to fix identified issues
- **Multiple Output Formats** - Compatible with Nuclei's JSON, YAML, and text outputs

### 🛠️ Installation & Usage

### Prerequisites
- [Nuclei](https://github.com/projectdiscovery/nuclei) (latest version recommended)
- Valid internet connection for AWS API calls

### Basic Usage

```bash
# Scan a single domain
nuclei -t s3-bucket-enum.yaml -var bucket=example.com

# Scan multiple domains from a file
nuclei -t s3-bucket-enum.yaml -l domains.txt

# Scan with verbose output
nuclei -t s3-bucket-enum.yaml -var bucket=example.com -v

# Output results to JSON
nuclei -t s3-bucket-enum.yaml -var bucket=example.com -json -o results.json
```

### Advanced Usage

```bash
# Scan with custom user agent
nuclei -t s3-bucket-enum.yaml -var bucket=example.com -H "User-Agent: MyScanner/1.0"

# Scan with rate limiting (recommended for large scans)
nuclei -t s3-bucket-enum.yaml -l domains.txt -rate-limit 10

# Scan only critical severity findings
nuclei -t s3-bucket-enum.yaml -var bucket=example.com -severity critical,high
```

### 📊 Risk Matrix

| Finding Type | Severity | Description |
|--------------|----------|-------------|
| `public_listing_enabled` | **CRITICAL** | Full bucket contents publicly exposed and listable |
| `public_no_listing` | **HIGH** | Bucket publicly readable but listing disabled |
| `sensitive_files` | **HIGH** | Sensitive files detected in public bucket |
| `website_enabled` | **MEDIUM** | S3 website hosting enabled |
| `redirect_detected` | **MEDIUM** | Redirect detected (possible website hosting) |
| `access_denied` | **LOW** | Bucket exists but properly secured |
| `not_found` | **INFO** | Bucket does not exist |

### 🔧 Configuration

### Customizing Regions
To modify the regions tested, edit the `regions` section:

```yaml
regions:
  - us-east-1
  - eu-west-1
  - ap-southeast-1
  # Add more regions as needed
```

### Adding Custom Naming Patterns
Extend the path list to include organization-specific patterns:

```yaml
path:
  - "https://{{bucket}}-custom.s3.amazonaws.com/"
  - "https://internal-{{bucket}}.s3.amazonaws.com/"
```

### Sensitive File Patterns
Customize the sensitive file detection regex:

```yaml
regex:
  - "<Key>.*?(\.env|custom-config|secret-file).*?</Key>"
```

### 📋 Sample Output

```
[CRITICAL] public_listing_enabled on https://example-backup.s3.amazonaws.com/
- Extracted 127 files including sensitive data
- Files: config.json, .env, database-backup.sql

[HIGH] sensitive_files detected on https://example-logs.s3.amazonaws.com/
- Found: application.properties, private-key.pem

[MEDIUM] website_enabled on https://example-static.s3-website-us-east-1.amazonaws.com/
- S3 website hosting is active
```

## 🛡️ Remediation Guide

### For Public Buckets (CRITICAL/HIGH)
1. **Enable Block Public Access**:
   ```bash
   aws s3api put-public-access-block --bucket BUCKET_NAME --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
   ```

2. **Review Bucket Policies**:
   ```bash
   aws s3api get-bucket-policy --bucket BUCKET_NAME
   ```

3. **Remove Sensitive Files**:
   - Audit and remove credentials, keys, and configuration files
   - Implement `.gitignore` patterns to prevent future exposure

### For All Buckets
1. **Enable Server-Side Encryption**:
   ```bash
   aws s3api put-bucket-encryption --bucket BUCKET_NAME --server-side-encryption-configuration Rules=[{ApplyServerSideEncryptionByDefault={SSEAlgorithm=AES256}}]
   ```

2. **Enable Logging**:
   ```bash
   aws s3api put-bucket-logging --bucket BUCKET_NAME --bucket-logging-status file://logging.json
   ```

3. **Implement IAM Policies**:
   - Use least-privilege access principles
   - Regular access reviews and rotation

### 🤔 Why This Project ?

This project was created to enhance a paid Nuclei template I found on a Telegram channel. The original template was designed to scan for exposed AWS S3 buckets, but it lacked several features I thought could make it more effective. So, I decided to build on top of it and add more detailed detection, better matchers, and extended support for various S3 misconfigurations.🗿

### 💓 Credits:

 * Credits go to the original author of the paid AWS S3 scanner template shared on that Telegram channel , without their work, this upgrade wouldn't exist.😂

### ⌚ Total Time Taken to Develop & Test

**25 min** - LOL XD 💀, Template is created by  **Sonnet 4 model of Anthropic**! which took 11 min. The rest of the time, 1 hour and 5 minutes , was spent writing the README and editing the templates.

### 📞 Support


📧 **Email**: pookielinuxuser@tutamail.com 

- For AWS-specific issues, refer to : AWS Security Guides


For Nuclei-specific issues, refer to:
- [Nuclei Documentation](https://nuclei.projectdiscovery.io/)
- [ProjectDiscovery GitHub](https://github.com/projectdiscovery/nuclei)


### 📜 License

Licensed under **Affero GNU General Public License 3.0**

### 🕒 Last Updated:
August 19, 2025
