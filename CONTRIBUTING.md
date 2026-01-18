# Contributing to Directory Payload List

First off, thank you for considering contributing to Directory Payload List! It's people like you that make this project such a great resource for the security community.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
  - [Reporting Bugs](#reporting-bugs)
  - [Suggesting New Payloads](#suggesting-new-payloads)
  - [Submitting Pull Requests](#submitting-pull-requests)
- [Payload Guidelines](#payload-guidelines)
- [Style Guide](#style-guide)
- [Commit Messages](#commit-messages)

## Code of Conduct

This project and everyone participating in it is governed by our commitment to creating a welcoming and inclusive environment. By participating, you are expected to uphold this commitment.

### Our Standards

- Be respectful and considerate
- Welcome newcomers and help them learn
- Focus on what is best for the community
- Show empathy towards other community members
- Use these payloads only for legal and ethical purposes

## How Can I Contribute?

### Reporting Bugs

If you find an issue with the payloads:

1. **Search existing issues** to see if it has already been reported
2. If not found, **create a new issue** with:
   - Clear, descriptive title
   - File name where the issue occurs
   - Description of the problem
   - Expected vs actual behavior
   - Any relevant examples

### Suggesting New Payloads

We're always looking to expand our payload collection!

**Before Submitting:**
- Check if the payload already exists in the relevant file
- Verify the payload works in real-world scenarios
- Ensure it follows our guidelines

**How to Submit:**
1. Open a new issue with the label "enhancement"
2. Provide the payload(s)
3. Explain the use case and target
4. Include any testing results or references

### Submitting Pull Requests

1. **Fork the repository** and create your branch from `main`
   ```bash
   git checkout -b feature/add-new-payloads
   ```

2. **Make your changes** following our guidelines

3. **Test your payloads** to ensure they work

4. **Commit your changes** with clear commit messages

5. **Push to your fork** and submit a pull request
   ```bash
   git push origin feature/add-new-payloads
   ```

6. **Wait for review** - we'll review your PR as soon as possible

## Payload Guidelines

### General Rules

✅ **DO:**
- Add payloads to the appropriate category file
- Keep entries in alphabetical order (unless order matters for testing)
- Remove duplicate entries
- Test payloads before submitting
- Add one entry per line
- Include variations that have proven useful
- Document unusual or complex payloads

❌ **DON'T:**
- Add malicious or destructive payloads
- Include payloads that cause DoS or resource exhaustion
- Submit payloads that violate laws or ethical standards
- Add excessively similar variations without reason
- Include comments or descriptions within payload files

### File Organization

Choose the correct file for your payload:

- **common-directories.txt** - Generic, frequently found directories
- **webserver-directories.txt** - Server configs, version control, env files
- **backup-sensitive.txt** - Backup files, dumps, credentials, logs
- **cms-directories.txt** - CMS-specific paths (WordPress, Joomla, etc.)
- **api-endpoints.txt** - API paths, REST/GraphQL, documentation
- **dev-test-directories.txt** - Development, staging, testing environments
- **cloud-platforms.txt** - Cloud service paths (AWS, Azure, GCP, etc.)
- **admin-panels.txt** - Admin interfaces, login pages, control panels
- **database-directories.txt** - Database tools, configs, backup locations
- **language-framework.txt** - Language/framework-specific paths
- **special-encoded.txt** - Path traversal, encoding variations
- **file-extensions.txt** - File extensions for content discovery

### Creating New Categories

If your payloads don't fit existing categories:

1. Create a new file in the `Intruder/` directory
2. Use lowercase with hyphens (e.g., `new-category.txt`)
3. Add a clear description in the README.md
4. Include at least 50 relevant payloads
5. Ensure it serves a distinct purpose

## Style Guide

### Formatting Rules

```
# Good Examples
admin
admin/login
api/v1/users
../../../etc/passwd
wp-content/plugins

# Bad Examples
admin  (trailing spaces)
admin/login/ (trailing slash - unless necessary)
/admin (leading slash - unless necessary)
ADMIN (wrong case - unless testing case sensitivity)
```

### Naming Conventions

- Use lowercase unless case sensitivity is being tested
- Use hyphens for word separation in directory names
- Use underscores where historically accurate (e.g., `wp_admin`)
- Keep consistency within each file

### Ordering

- **Alphabetical ordering** is preferred for most files
- **Logical ordering** may be used for:
  - Path traversal sequences (shortest to longest)
  - Version-specific paths (oldest to newest)
  - Encoding variations (simple to complex)

## Commit Messages

Write clear and meaningful commit messages:

### Format

```
<type>: <subject>

<body (optional)>
```

### Types

- `add:` Adding new payloads
- `update:` Updating existing payloads
- `remove:` Removing payloads
- `fix:` Fixing errors or issues
- `docs:` Documentation changes
- `refactor:` Reorganizing without changing content

### Examples

```
add: Include Laravel framework paths to language-framework.txt

add: Add 50 new cloud-specific endpoints for AWS Lambda

fix: Remove duplicate entries from admin-panels.txt

update: Add modern Next.js paths to cms-directories.txt

docs: Update usage examples for ffuf in README
```

## Testing Your Contributions

Before submitting, please verify:

1. **No Duplicates**
   ```bash
   sort file.txt | uniq -d
   ```

2. **No Trailing Spaces**
   ```bash
   grep -n ' $' file.txt
   ```

3. **Proper Line Endings** (Unix LF, not Windows CRLF)
   ```bash
   dos2unix file.txt
   ```

4. **Alphabetical Order** (if required)
   ```bash
   sort -c file.txt
   ```

## Review Process

1. **Initial Review** - Maintainer checks basic requirements
2. **Technical Review** - Payload effectiveness and accuracy
3. **Testing** - If possible, payloads are tested
4. **Merge** - Approved changes are merged into main branch

### Review Timeline

- Small changes: 1-3 days
- Large contributions: 3-7 days
- New categories: Up to 14 days

## Questions?

Feel free to:
- Open an issue with the `question` label
- Start a discussion in the repository
- Reach out to maintainers

## Recognition

Contributors will be acknowledged in:
- GitHub contributors list
- Release notes for significant contributions
- README acknowledgments section

## Legal Note

By contributing, you agree that:
- Your contributions will be licensed under the MIT License
- You have the right to submit the contribution
- Your contributions are for legal security testing purposes
- You understand the ethical implications

---

**Thank you for contributing to the security community!** 🙏

Every payload you add helps security researchers, penetration testers, and bug bounty hunters worldwide.