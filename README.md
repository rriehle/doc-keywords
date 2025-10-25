# doc-keywords

A standalone Babashka tool for extracting, analyzing, and validating keywords in Markdown documentation. Supports keyword indexing, relationship graphing, validation against taxonomy, and cross-project analysis.

## Deprecation Notice

This repo has been deprecated in favor of the .doc utilities, which splits out functionality into multiple scripts which each "do one thing, and do it well."

## Features

- **Extract keywords** from individual Markdown files
- **Build keyword indexes** showing which documents contain which keywords
- **Generate relationship graphs** visualizing keyword connections
- **Validate keywords** against a project-specific taxonomy
- **Suggest keywords** based on document content
- **Usage statistics** across single or multiple projects
- **Cross-project analysis** for comparing keyword usage across codebases
- **Project-aware** with optional configuration per project
- **Zero dependencies** - single executable script using Babashka

## Installation

### Prerequisites

- [Babashka](https://babashka.org/) installed on your system

### Option 1: Clone and Link

```bash
git clone <repository-url> ~/bin/doc-keywords
# Add ~/bin/doc-keywords to your PATH
echo 'export PATH="$HOME/bin/doc-keywords:$PATH"' >> ~/.bashrc  # or ~/.zshrc
source ~/.bashrc  # or source ~/.zshrc
```

### Option 2: Clone and Symlink

```bash
git clone <repository-url> ~/tools/doc-keywords
ln -s ~/tools/doc-keywords/doc-keywords ~/bin/doc-keywords
# Ensure ~/bin is in your PATH
```

### Option 3: Direct Download

```bash
curl -o ~/bin/doc-keywords <raw-url-to-script>
chmod +x ~/bin/doc-keywords
```

## Quick Start

```bash
# Get help
doc-keywords help

# Extract keywords from a file
doc-keywords extract-keywords doc/README.md

# Get statistics for current project
cd ~/my-project
doc-keywords keyword-stats

# Validate keywords against taxonomy
doc-keywords validate-keywords

# Build keyword index as JSON
doc-keywords keyword-index > keywords.json

# Generate DOT graph
doc-keywords keyword-graph > keywords.dot
dot -Tpng keywords.dot -o keywords.png
```

## Keyword Syntax

Keywords follow Clojure-style conventions and are embedded in Markdown using bracket notation:

```markdown
Single keyword: [:architecture]
Multiple keywords: [:security :authentication :encryption]
```

## Commands

### extract-keywords

Extract all keywords from a specific Markdown file.

```bash
doc-keywords extract-keywords path/to/file.md
```

**Output:** List of keywords, one per line

### keyword-index

Build a JSON index showing which documents contain which keywords.

```bash
# Current project
doc-keywords keyword-index

# Specific project
doc-keywords keyword-index ~/projects/my-app

# Multiple projects (cross-project index)
doc-keywords keyword-index ~/proj1 ~/proj2 ~/proj3
```

**Output:** JSON object mapping keywords to arrays of file paths

**Example:**
```json
{
  "security": ["components/auth.md", "architecture/overview.md"],
  "architecture": ["architecture/overview.md", "doc/design.md"]
}
```

### keyword-graph

Generate a DOT format graph showing keyword relationships. Keywords that appear together in the same bracket set are considered related.

```bash
# Current project
doc-keywords keyword-graph > keywords.dot

# Multiple projects
doc-keywords keyword-graph ~/proj1 ~/proj2 > keywords.dot

# Generate PNG
doc-keywords keyword-graph > keywords.dot
dot -Tpng keywords.dot -o keywords.png
```

**Output:** GraphViz DOT format

### validate-keywords

Validate all keywords used in documentation against the project's taxonomy file.

```bash
# Current project
doc-keywords validate-keywords

# Specific project
doc-keywords validate-keywords ~/projects/my-app

# Multiple projects (validates each separately)
doc-keywords validate-keywords ~/proj1 ~/proj2
```

**Output:** Success message or list of invalid keywords by file

**Exit codes:**
- 0: All keywords valid
- 1: Invalid keywords found

### suggest-keywords

Analyze a file's content and suggest relevant keywords based on common patterns.

```bash
doc-keywords suggest-keywords path/to/file.md
```

**Output:** Suggested keywords based on content analysis

### keyword-stats

Show usage statistics for all keywords across projects.

```bash
# Current project
doc-keywords keyword-stats

# Specific project
doc-keywords keyword-stats ~/projects/my-app

# Multiple projects (combined statistics)
doc-keywords keyword-stats ~/proj1 ~/proj2 ~/proj3
```

**Output:** Keyword usage counts, sorted by frequency

## Configuration

### Project-Level Configuration

Create `.doc-keywords.edn` in your project root to customize behavior:

```edn
{:doc-dir "doc"
 :taxonomy "doc-tools/keyword-taxonomy.md"}
```

**Configuration options:**

- `:doc-dir` - Directory containing Markdown documentation (default: `"doc"`)
- `:taxonomy` - Path to keyword taxonomy file (default: `"doc-tools/keyword-taxonomy.md"`)

### Defaults

If no `.doc-keywords.edn` is present, the tool uses these defaults:

- Documentation directory: `doc/`
- Taxonomy file: `doc-tools/keyword-taxonomy.md`

### Project Discovery

The tool automatically discovers your project root by:

1. Looking for a git repository (searches upward from current directory)
2. Falling back to the current working directory if not in a git repo

All paths in the config are resolved relative to the project root.

## Taxonomy File Format

The taxonomy file defines valid keywords for your project. Keywords should be documented using backtick-colon notation:

```markdown
# Keyword Taxonomy

## Architecture
- `:architecture` - Architectural decisions and patterns
- `:modularity` - Module boundaries and organization
- `:abstraction` - Abstraction layers and interfaces

## Security
- `:security` - General security concerns
- `:authentication` - Authentication mechanisms
- `:authorization` - Authorization and access control
```

See `example-taxonomy.md` for a complete example.

## Usage Examples

### Single Project Workflow

```bash
cd ~/my-project

# Create taxonomy
mkdir -p doc-tools
cat > doc-tools/keyword-taxonomy.md << 'EOF'
# Project Keywords

- `:architecture` - Architecture decisions
- `:security` - Security concerns
- `:api` - API design
EOF

# Add keywords to your docs
echo "This document covers [:architecture :api] design." > doc/design.md

# Validate
doc-keywords validate-keywords
# Output: ✓ All keywords valid

# Get statistics
doc-keywords keyword-stats
# Output: Keyword usage statistics...
```

### Cross-Project Analysis

Compare keyword usage across multiple projects:

```bash
# Analyze three projects
doc-keywords keyword-stats ~/work/api ~/work/frontend ~/work/backend

# Build unified index
doc-keywords keyword-index ~/work/* > all-projects-index.json

# Generate relationship graph across projects
doc-keywords keyword-graph ~/work/* > all-keywords.dot
```

### CI/CD Integration

Validate keywords in continuous integration:

```bash
# In .github/workflows/docs.yml or similar
- name: Validate documentation keywords
  run: doc-keywords validate-keywords
```

The command exits with code 1 if validation fails, making it suitable for CI pipelines.

### Custom Documentation Structure

If your docs are in a different location:

```edn
;; .doc-keywords.edn
{:doc-dir "docs"
 :taxonomy "docs/taxonomy.md"}
```

```bash
doc-keywords validate-keywords
# Uses docs/ directory and docs/taxonomy.md
```

## Comparison to Original bb.edn

This tool extracts and generalizes the keyword utilities from the original `doc-tools/bb.edn` format:

**Original (project-specific):**
```bash
cd project
bb keyword-index
bb validate-keywords
```

**New (standalone, reusable):**
```bash
doc-keywords keyword-index ~/project
doc-keywords validate-keywords ~/project
```

**Key improvements:**
- Single executable script (no bb.edn per project)
- Works across multiple projects
- Project-specific configuration via `.doc-keywords.edn`
- Cross-project analysis built-in
- Standalone with no external dependencies

## Troubleshooting

### "Taxonomy file not found"

Ensure your taxonomy file exists at the configured location (default: `doc-tools/keyword-taxonomy.md`). You can customize this in `.doc-keywords.edn`:

```edn
{:taxonomy "docs/keywords.md"}
```

### No keywords found

Check that:
1. Your Markdown files use the correct syntax: `[:keyword]` or `[:keyword1 :keyword2]`
2. Files are in the configured doc directory (default: `doc/`)
3. Files have `.md` extension

### Permission denied

Ensure the script is executable:

```bash
chmod +x ~/bin/doc-keywords/doc-keywords
```

## Development

The tool is a single Babashka script with no external dependencies beyond Babashka's built-in libraries:

- `babashka.fs` - File system operations
- `clojure.edn` - Configuration parsing
- `clojure.string` - String manipulation
- `clojure.java.io` - I/O operations
- `cheshire.core` - JSON generation

To modify:

1. Edit `doc-keywords` script directly
2. Test with: `bb doc-keywords <command> [args]`
3. No compilation or build step required

## License

MIT

## Contributing

Will review Pull Requests

