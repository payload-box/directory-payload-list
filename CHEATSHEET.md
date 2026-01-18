# Directory Payload List - Quick Reference Cheat Sheet

## 🚀 Quick Start Commands

### Burp Suite Intruder
```
1. Send request to Intruder
2. Set position: GET /§payload§ HTTP/1.1
3. Payloads tab → Load → Select file from Intruder/
4. Start attack
```

### ffuf
```bash
# Basic scan
ffuf -w Intruder/common-directories.txt -u https://target.com/FUZZ

# With status code filter
ffuf -w Intruder/api-endpoints.txt -u https://target.com/FUZZ -mc 200,301,302,401,403

# Recursive scanning
ffuf -w Intruder/common-directories.txt -u https://target.com/FUZZ -recursion -recursion-depth 2

# Multiple wordlists
ffuf -w Intruder/common-directories.txt:DIRS -w Intruder/file-extensions.txt:EXTS -u https://target.com/DIRS.EXTS
```

### gobuster
```bash
# Directory enumeration
gobuster dir -u https://target.com -w Intruder/common-directories.txt -t 50

# With extensions
gobuster dir -u https://target.com -w Intruder/backup-sensitive.txt -x php,txt,bak,zip

# Hide 404s
gobuster dir -u https://target.com -w Intruder/admin-panels.txt -b 404
```

### dirsearch
```bash
# Basic scan
dirsearch -u https://target.com -w Intruder/common-directories.txt

# With extensions
dirsearch -u https://target.com -w Intruder/cms-directories.txt -e php,html,js

# Multiple targets
dirsearch -l targets.txt -w Intruder/api-endpoints.txt --format=json -o results.json
```

### wfuzz
```bash
# Basic fuzzing
wfuzz -w Intruder/common-directories.txt https://target.com/FUZZ

# Filter by status code
wfuzz -w Intruder/admin-panels.txt --sc 200,301,302,401,403 https://target.com/FUZZ

# Hide 404s
wfuzz -w Intruder/dev-test-directories.txt --hc 404 https://target.com/FUZZ
```

### curl (Manual Testing)
```bash
# Test single path
curl -I https://target.com/admin

# Test with loop
while read path; do curl -so /dev/null -w "%{http_code} - $path\n" https://target.com/$path; done < Intruder/common-directories.txt
```

## 📊 Payload Selection Guide

### Initial Reconnaissance
```
1st: common-directories.txt (206 payloads)
2nd: webserver-directories.txt (101 payloads)
```

### Technology Identified?
```
WordPress/CMS → cms-directories.txt (329 payloads)
API/REST     → api-endpoints.txt (254 payloads)
Cloud        → cloud-platforms.txt (251 payloads)
Database     → database-directories.txt (277 payloads)
```

### Deep Dive
```
Admin Access → admin-panels.txt (299 payloads)
Backups      → backup-sensitive.txt (184 payloads)
Dev/Test     → dev-test-directories.txt (239 payloads)
```

### Advanced Testing
```
Path Traversal → special-encoded.txt (308 payloads)
File Discovery → file-extensions.txt (342 payloads)
Stack Detection → language-framework.txt (464 payloads)
```

## 🎯 Common Scenarios

### Scenario 1: New Target (No Info)
```bash
# Step 1: Quick scan
ffuf -w Intruder/common-directories.txt -u https://target.com/FUZZ -mc all -fc 404

# Step 2: Identify technology from results
# Found /wp-admin? → It's WordPress
# Found /api/? → It has an API

# Step 3: Technology-specific scan
ffuf -w Intruder/cms-directories.txt -u https://target.com/FUZZ -mc all -fc 404
```

### Scenario 2: WordPress Site
```bash
# Admin areas
gobuster dir -u https://target.com -w Intruder/cms-directories.txt -t 50

# Plugins
ffuf -w Intruder/cms-directories.txt -u https://target.com/FUZZ -mc 200,301,403

# Backups
wfuzz -w Intruder/backup-sensitive.txt --hc 404 https://target.com/FUZZ
```

### Scenario 3: API Testing
```bash
# Discover endpoints
ffuf -w Intruder/api-endpoints.txt -u https://target.com/FUZZ -mc all -fc 404

# Version testing
ffuf -w <(seq 1 10) -u https://target.com/api/vFUZZ -mc all -fc 404

# Documentation
curl https://target.com/api/docs
curl https://target.com/swagger.json
```

### Scenario 4: Cloud Application
```bash
# AWS paths
ffuf -w Intruder/cloud-platforms.txt -u https://target.com/FUZZ -mc all -fc 404

# Serverless functions
ffuf -w Intruder/api-endpoints.txt -u https://target.com/FUZZ -mc all -fc 404
```

### Scenario 5: Looking for Backups
```bash
# Direct backup scan
ffuf -w Intruder/backup-sensitive.txt -u https://target.com/FUZZ -mc 200,403

# With common extensions
gobuster dir -u https://target.com -w Intruder/backup-sensitive.txt -x zip,sql,bak,tar.gz,rar
```

### Scenario 6: Path Traversal Testing
```bash
# Basic traversal
ffuf -w Intruder/special-encoded.txt -u https://target.com/download?file=FUZZ/etc/passwd

# Parameter fuzzing
wfuzz -w Intruder/special-encoded.txt https://target.com/file.php?path=FUZZ
```

## 🔧 Tool-Specific Tips

### ffuf
- `-mc all` - Match all status codes
- `-fc 404` - Filter out 404s
- `-fs 0` - Filter by response size
- `-t 100` - Set threads (default: 40)
- `-rate 100` - Requests per second
- `-recursion` - Enable recursive scanning
- `-o output.json` - Save results

### gobuster
- `-t 50` - Number of threads
- `-x php,txt` - File extensions
- `-s "200,204,301,302,307,401,403"` - Status codes
- `-b 404` - Exclude status code
- `-k` - Skip SSL verification
- `--timeout 10s` - Request timeout

### dirsearch
- `-t 50` - Number of threads
- `-e php,html` - Extensions
- `-x 404,403` - Exclude status codes
- `-r` - Recursive scanning
- `--format=json` - Output format
- `--random-agent` - Random user agent

### wfuzz
- `--hc 404` - Hide 404 responses
- `--sc 200` - Show only 200 responses
- `--hl 10` - Hide by line count
- `--hw 100` - Hide by word count
- `--hh 1000` - Hide by char count
- `-t 100` - Concurrent connections

## ⚡ Performance Tuning

### Fast Scan (Noisy)
```bash
ffuf -w Intruder/common-directories.txt -u https://target.com/FUZZ -t 200 -rate 500
```

### Balanced Scan (Recommended)
```bash
ffuf -w Intruder/common-directories.txt -u https://target.com/FUZZ -t 50 -rate 100
```

### Stealth Scan (Slow)
```bash
ffuf -w Intruder/common-directories.txt -u https://target.com/FUZZ -t 10 -rate 10 -p 1-3
```

## 🎨 Response Analysis

### Interesting Status Codes
```
200 - OK (Found!)
201 - Created
204 - No Content
301 - Moved Permanently (Redirect)
302 - Found (Temporary Redirect)
307 - Temporary Redirect
401 - Unauthorized (Auth required)
403 - Forbidden (Exists but no access)
405 - Method Not Allowed (Try different method)
500 - Internal Server Error (Potential bug)
```

### What to Look For
```
Size: Unusual response sizes
Time: Slow responses may indicate processing
Headers: X-Powered-By, Server, etc.
Content: Error messages, stack traces
```

## 📋 File Count Reference
```
common-directories.txt       206 payloads
webserver-directories.txt    101 payloads
backup-sensitive.txt         184 payloads
cms-directories.txt          329 payloads
api-endpoints.txt            254 payloads
dev-test-directories.txt     239 payloads
cloud-platforms.txt          251 payloads
admin-panels.txt             299 payloads
database-directories.txt     277 payloads
language-framework.txt       464 payloads
special-encoded.txt          308 payloads
file-extensions.txt          342 payloads
----------------------------------------
TOTAL:                      3,254 payloads
```

## 🛡️ Ethical Testing Checklist

- [ ] I have written permission to test
- [ ] Target is in scope
- [ ] Rate limiting is configured
- [ ] Not causing DoS/resource exhaustion
- [ ] Following rules of engagement
- [ ] Will report findings responsibly

## 💡 Pro Tips

1. **Start Small**: Use `common-directories.txt` first
2. **Filter Noise**: Always filter 404s and error pages
3. **Follow Redirects**: Check where 301/302 redirects lead
4. **Check Source Code**: Sometimes paths are in HTML/JS
5. **Combine Lists**: Merge relevant lists for comprehensive testing
6. **Monitor Traffic**: Use Burp Suite to see all requests/responses
7. **Check Robots.txt**: Often reveals interesting paths
8. **Look for Patterns**: If you find `/api/v1`, try `/api/v2`, `/api/v3`
9. **Test Methods**: Try GET, POST, PUT, DELETE on found endpoints
10. **Document Everything**: Keep notes of what you find

## 🔗 Quick Links

- Repository: https://github.com/payload-box/directory-payload-list
- Issues: https://github.com/payload-box/directory-payload-list/issues
- Contributing: See CONTRIBUTING.md
- License: MIT

---

**Remember**: Always test ethically and legally! 🎯