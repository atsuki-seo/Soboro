> 🇯🇵 [日本語版はこちら](CONTRIBUTING.ja.md)

# Contributing to Soboro

Thank you for your interest in contributing to the Soboro project!

## Introduction

Soboro is an experimental OS project that builds a userland in Swift.
Contributions from everyone are welcome.

## How to Contribute

### Reporting Issues

- Please use GitHub Issues for bug reports and feature proposals
- Both Japanese and English are welcome
- Following the issue template is appreciated

### Pull Requests

1. Fork the repository
2. Create a branch (`feature/{component}/{description}` or `fix/{component}/{description}`)
3. Commit your changes
4. Create a Pull Request

### Development Workflow

```bash
# Clone your forked repository
git clone https://github.com/YOUR_NAME/soboro.git
cd soboro

# Create a development branch
git checkout -b feature/init/your-feature

# Develop...

# Test
swift build
swift test

# Commit (English, conventional commits format)
git commit -m "feat(init): add your feature"

# Push and create PR
git push origin feature/init/your-feature
```

## Coding Standards

- **Code style**: Follow the naming conventions in `README.md`
- **When using Claude Code**: Refer to `CLAUDE.md`
- **Tests**: Add tests for new features
- **Documentation**: Add DocC comments to public APIs

## Commit Messages

```
{type}({scope}): {description}
```

- **type**: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`
- **scope**: Component name (`init`, `compositor`, `foundation`, etc.)
- **description**: Brief description in English

Example: `feat(networkd): add DHCP client support`

## Multilingual Documentation

When creating design documents or ADRs, please provide both Japanese and English versions.

```
docs/decisions/
├── ja/ADR-0001-your-decision.md  # Japanese version
└── en/ADR-0001-your-decision.md  # English version
```

## Communication

- **Issues / PR**: Both Japanese and English are welcome
- **Commit messages**: English
- **Code comments**: English

## Code of Conduct

- Treat each other with respect
- Provide constructive feedback
- Beginner questions are welcome

## License

Contributed code will be published under the MIT License.

---

Feel free to ask questions via Issues!
