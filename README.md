# Directory Payload List 🔍

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Maintained: Yes](https://img.shields.io/badge/Maintained-Yes-green.svg)](https://github.com/payload-box/directory-payload-list)
[![Contributions Welcome](https://img.shields.io/badge/Contributions-Welcome-brightgreen.svg)](CONTRIBUTING.md)

A comprehensive collection of directory and path payloads for web application security testing, penetration testing, and bug bounty hunting. This repository contains carefully curated wordlists designed to discover hidden directories, files, and endpoints during web application reconnaissance and enumeration.

## 📋 Table of Contents

- [Overview](#overview)
- [Payload Categories](#payload-categories)
- [Installation](#installation)
- [Usage](#usage)
  - [With Burp Suite Intruder](#with-burp-suite-intruder)
  - [With ffuf](#with-ffuf)
  - [With dirsearch](#with-dirsearch)
  - [With gobuster](#with-gobuster)
  - [With wfuzz](#with-wfuzz)
- [Payload Descriptions](#payload-descriptions)
- [Best Practices](#best-practices)
- [Contributing](#contributing)
- [Legal Disclaimer](#legal-disclaimer)
- [License](#license)

## 🎯 Overview

This repository provides organized and categorized payload lists specifically designed for directory and file enumeration during web application security assessments. Each payload list is carefully crafted to target specific technologies, frameworks, and common misconfigurations.

### Key Features

- ✅ **Organized Categories**: Payloads are grouped by technology, purpose, and target
- ✅ **Regular Updates**: Continuously updated with new patterns and discoveries
- ✅ **Burp Suite Ready**: All payloads are formatted for direct use with Burp Suite Intruder
- ✅ **Tool Agnostic**: Compatible with all major enumeration tools
- ✅ **No Duplicates**: Each list is deduplicated and optimized
- ✅ **Real-World Tested**: Payloads derived from actual penetration testing scenarios

## 📁 Payload Categories

### Core Payload Files

| File | Description | Count | Best For |
|------|-------------|-------|----------|
| **common-directories.txt** | Most common web directories and paths | 206 | Initial reconnaissance, quick scans |
| **webserver-directories.txt** | Web server specific paths and config files | 101 | Server configuration discovery, version detection |
| **backup-sensitive.txt** | Backup files and sensitive data locations | 184 | Finding exposed backups, credentials, logs |
| **cms-directories.txt** | CMS-specific paths (WordPress, Joomla, Drupal, etc.) | 329 | CMS identification and exploitation |
| **api-endpoints.txt** | API paths, REST/GraphQL endpoints, documentation | 254 | API discovery, endpoint enumeration |
| **dev-test-directories.txt** | Development and testing environment paths | 239 | Finding dev/staging environments |
| **cloud-platforms.txt** | Cloud service paths (AWS, Azure, GCP, etc.) | 251 | Cloud infrastructure discovery |
| **admin-panels.txt** | Admin panel locations and login pages | 299 | Access control testing, authentication bypass |
| **database-directories.txt** | Database-related paths and management tools | 277 | Database exposure, admin interfaces |
| **language-framework.txt** | Programming language and framework specific paths | 464 | Technology stack identification |
| **special-encoded.txt** | Path traversal, encoding variations | 308 | Path traversal attacks, filter bypass |
| **file-extensions.txt** | Common file extensions for content discovery | 342 | File type enumeration, upload testing |

## 🚀 Installation

### Clone the Repository

```bash
git clone https://github.com/payload-box/directory-payload-list.git
cd directory-payload-list
```

### Quick Start

```bash
# Navigate to the Intruder directory
cd Intruder

# List all available payloads
ls -la

# View a specific payload file
cat common-directories.txt
```

## 💻 Usage

### With Burp Suite Intruder

1. **Configure Intruder Position**
   - Send a request to Burp Suite Intruder
   - Set the injection point: `GET /§payload§ HTTP/1.1`

2. **Load Payloads**
   - Go to the "Payloads" tab
   - Click "Load" and select a payload file from the `Intruder/` directory
   - Start the attack

3. **Recommended Settings**
   - Payload type: Simple list
   - Payload processing: URL-encode special characters (if needed)
   - Grep - Match: Add common success indicators (200, 301, 302, 403)

### With ffuf

```bash
# Basic directory fuzzing
ffuf -w Intruder/common-directories.txt -u https://target.com/FUZZ

# With custom wordlist and filters
ffuf -w Intruder/api-endpoints.txt -u https://target.com/FUZZ \
  -mc 200,301,302,401,403 -fs 0

# Multiple wordlists
ffuf -w Intruder/common-directories.txt:DIRS \
     -w Intruder/file-extensions.txt:EXTS \
     -u https://target.com/DIRS.EXTS

# Recursive scanning
ffuf -w Intruder/common-directories.txt -u https://target.com/FUZZ \
  -recursion -recursion-depth 2
```

### With dirsearch

```bash
# Basic scan
dirsearch -u https://target.com -w Intruder/common-directories.txt

# CMS-specific scan
dirsearch -u https://target.com -w Intruder/cms-directories.txt \
  -e php,html,js,txt

# Multiple targets
dirsearch -l targets.txt -w Intruder/api-endpoints.txt \
  --format=json -o results.json
```

### With gobuster

```bash
# Directory enumeration
gobuster dir -u https://target.com \
  -w Intruder/common-directories.txt \
  -t 50 -s "200,204,301,302,307,401,403"

# API endpoint discovery
gobuster dir -u https://target.com \
  -w Intruder/api-endpoints.txt \
  -p pattern.txt

# With extensions
gobuster dir -u https://target.com \
  -w Intruder/backup-sensitive.txt \
  -x php,txt,bak,old,zip
```

### With wfuzz

```bash
# Basic fuzzing
wfuzz -w Intruder/common-directories.txt \
  https://target.com/FUZZ

# Filter by response code
wfuzz -w Intruder/admin-panels.txt \
  --sc 200,301,302,401,403 \
  https://target.com/FUZZ

# Hide specific response codes
wfuzz -w Intruder/dev-test-directories.txt \
  --hc 404 \
  https://target.com/FUZZ
```

## 📖 Payload Descriptions

### common-directories.txt
Contains the most frequently encountered directory names in web applications. Perfect for initial reconnaissance and quick scans. Includes standard paths like `/admin`, `/api`, `/upload`, `/backup`, etc.

### webserver-directories.txt
Targets web server-specific files and directories including configuration files, version control systems, environment files, and common server management interfaces like phpMyAdmin, server-status, and various admin consoles.

### backup-sensitive.txt
Focuses on discovering backup files, database dumps, configuration backups, sensitive files, credentials, and logs. Essential for finding exposed sensitive data and security misconfigurations.

### cms-directories.txt
Comprehensive list of paths for popular Content Management Systems including WordPress, Joomla, Drupal, Magento, PrestaShop, Typo3, and many others. Includes plugin directories, theme paths, and admin interfaces.

### api-endpoints.txt
Targets modern API architectures including REST, GraphQL, SOAP endpoints, API documentation (Swagger, OpenAPI), health checks, webhooks, and various API versioning patterns.

### dev-test-directories.txt
Identifies development, testing, staging, and debugging environments. Useful for finding pre-production systems, test data, debug interfaces, and experimental features.

### cloud-platforms.txt
Cloud-specific paths for AWS, Azure, GCP, Firebase, Heroku, and other cloud platforms. Includes service-specific endpoints, management interfaces, and cloud storage paths.

### admin-panels.txt
Extensive collection of admin panel locations, login pages, control panels, and administrative interfaces across different technologies and languages.

### database-directories.txt
Database-related paths including database management tools (phpMyAdmin, Adminer), database backup locations, database configuration files, and various database technologies.

### language-framework.txt
Technology stack-specific paths for programming languages and frameworks (PHP, Python, Java, Node.js, Ruby, .NET, Go, and many others). Helps identify the underlying technology.

### special-encoded.txt
Advanced payload list featuring path traversal sequences, encoding variations, null byte injections, and special character combinations for filter bypass and security testing.

### file-extensions.txt
Comprehensive list of file extensions for content discovery, upload testing, and identifying different file types in web applications.

## 🎓 Best Practices

### Reconnaissance Strategy

1. **Start Broad**: Begin with `common-directories.txt` for initial reconnaissance
2. **Identify Technology**: Use responses to determine the technology stack
3. **Target Specific**: Switch to technology-specific lists (CMS, framework, etc.)
4. **Deep Dive**: Use specialized lists like `backup-sensitive.txt` or `api-endpoints.txt`
5. **Bypass Filters**: Employ `special-encoded.txt` if encountering input validation

### Performance Optimization

- **Rate Limiting**: Always respect rate limits and implement delays
- **Thread Control**: Start with fewer threads (10-20) and increase gradually
- **Response Filtering**: Filter out noise (404s, error pages) to focus on interesting results
- **Time Management**: Use timeouts to avoid hanging on slow responses

### Ethical Testing

- ✅ Only test applications you have explicit permission to test
- ✅ Follow coordinated disclosure practices
- ✅ Respect bug bounty program rules and scope
- ✅ Don't perform DoS attacks or resource exhaustion
- ✅ Report vulnerabilities responsibly

## 🤝 Contributing

Contributions are welcome! If you have additional payloads, improvements, or bug fixes:

1. Fork the repository
2. Create a new branch (`git checkout -b feature/new-payloads`)
3. Add your payloads with proper categorization
4. Ensure no duplicates exist
5. Commit your changes (`git commit -m 'Add new payloads'`)
6. Push to the branch (`git push origin feature/new-payloads`)
7. Open a Pull Request

### Contribution Guidelines

- Maintain alphabetical ordering within files
- One entry per line
- Remove duplicates before submitting
- Add description to README if adding new category
- Test payloads before submitting

## ⚖️ Legal Disclaimer

This repository is intended for **educational purposes** and **authorized security testing only**. 

**Important**: 
- Only use these payloads on systems you own or have explicit written permission to test
- Unauthorized access to computer systems is illegal
- The authors and contributors are not responsible for misuse or damage caused by this tool
- Always comply with local laws and regulations
- Follow responsible disclosure practices

By using this repository, you agree to use it responsibly and ethically.

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Thanks to the security research community
- Inspired by various enumeration tools and wordlists
- Contributors and bug bounty hunters who helped improve these lists

## 📬 Contact

- **GitHub Issues**: [Report bugs or request features](https://github.com/payload-box/directory-payload-list/issues)
- **Pull Requests**: [Contribute to the project](https://github.com/payload-box/directory-payload-list/pulls)

## 🌟 Star History

If you find this project useful, please consider giving it a star ⭐

---

**Happy Hunting! 🎯**

*Remember: With great power comes great responsibility. Always test ethically and legally.*