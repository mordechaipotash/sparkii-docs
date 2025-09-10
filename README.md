# Sparkii Documentation System

A comprehensive documentation repository for the Sparkii Command Center ecosystem.

## 📁 Structure

```
Sparkii_Docs/
├── .claude/           # Claude Code custom commands
├── database/          # Database documentation
├── frameworks/        # Framework documentation (SHELET, etc.)
├── mordesides/        # Personal insights and analysis
├── projects/          # Project-specific documentation
├── reports/           # Generated reports and analysis
├── sandbox/           # Experimental documentation
├── tools/             # Tool documentation and guides
└── wiki/              # Knowledge base articles
```

## 🔗 Integration

This repository is integrated as a Git submodule in the main [sparkii-command-center](https://github.com/mordechaipotash/sparkii-command-center) repository.

### Working with this Submodule

**From the parent repository:**
```bash
# Clone with submodules
git clone --recursive https://github.com/mordechaipotash/sparkii-command-center.git

# Or initialize after cloning
git submodule init
git submodule update

# Update to latest version
cd Sparkii_Docs
git pull origin main
cd ..
git add Sparkii_Docs
git commit -m "Update Sparkii_Docs submodule"
```

**Standalone development:**
```bash
# Clone directly
git clone https://github.com/mordechaipotash/sparkii-docs.git

# Make changes
git add .
git commit -m "Your changes"
git push origin main
```

## 📚 Documentation Categories

### Frameworks
- **SHELET**: Complete framework documentation and implementation guides
- **COGNITIVE-TRANSLATION**: Discovery and analysis from chat histories
- **LEADING-TO-PEOPLE**: Leadership and communication frameworks

### Projects
- **WOTCFY**: Work Opportunity Tax Credit system documentation
- **mordechai-plus**: Personal website and portfolio documentation
- **sparkii-command-center**: Main dashboard documentation

### Tools
- Claude Code configurations and reports
- MCP server documentation
- Supabase, Vercel, and other tool guides

### Wiki
Knowledge base articles covering:
- Business frameworks
- System architectures
- Personal development systems
- Protocol documentation

## 🚀 Purpose

This documentation system serves as:
1. **Knowledge Repository**: Central location for all Sparkii-related documentation
2. **Reference Guide**: Quick access to frameworks, patterns, and best practices
3. **Historical Record**: Evolution of ideas and implementations
4. **Learning Resource**: Guides and tutorials for tools and systems

## 🔒 Security

- Sensitive configuration files (`.mcp.json`) are excluded via `.gitignore`
- No credentials or API keys should be committed
- Use environment variables for any sensitive data

## 📝 Contributing

1. Keep documentation organized in appropriate directories
2. Use clear, descriptive filenames
3. Include metadata (date, purpose) in documents
4. Follow Markdown best practices
5. Update this README when adding new categories

## 📦 Independence

While integrated with sparkii-command-center, this documentation can:
- Be used by other Sparkii projects
- Have its own versioning and releases
- Be developed independently
- Maintain its own commit history

---

Last Updated: 2025-09-10
Repository: https://github.com/mordechaipotash/sparkii-docs