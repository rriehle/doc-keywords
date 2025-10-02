# Keyword Taxonomy Example

This example demonstrates how to structure a keyword taxonomy for your documentation. Each project should maintain its own taxonomy reflecting its specific domain and architecture.

## Overview

Keywords follow Clojure-style syntax (`:keyword-name`) and enable cross-referencing concepts throughout documentation. They support both human navigation and programmatic analysis.

## Usage in Markdown

Keywords appear inline within markdown text using bracket notation:

```markdown
The authentication system [:security :authentication] handles user credentials...
Multiple related concepts [:api :rest :versioning] can be tagged together...
```

## Example Categories and Keywords

### Architectural Patterns
- `:architecture` - Overarching architectural decisions and patterns
- `:modularity` - Module boundaries and organization
- `:abstraction` - Abstraction layers and interfaces
- `:integration` - Integration points and patterns
- `:separation-of-concerns` - Separation of concerns principle

### Security
- `:security` - General security concerns
- `:authentication` - Authentication mechanisms
- `:authorization` - Authorization and access control
- `:encryption` - Encryption and cryptographic operations
- `:audit` - Audit trails and logging

### System Qualities
- `:performance` - Performance characteristics and optimization
- `:scalability` - Scaling patterns and limits
- `:reliability` - Reliability and fault tolerance
- `:maintainability` - Code maintainability and technical debt
- `:testability` - Testing strategies and coverage

### Implementation
- `:api` - API design and contracts
- `:rest` - REST architectural style
- `:database` - Database design and operations
- `:caching` - Caching strategies
- `:configuration` - Configuration management
- `:deployment` - Deployment patterns

### Process
- `:workflow` - Business workflows and processes
- `:validation` - Validation and verification
- `:versioning` - Version control and management
- `:migration` - Migration strategies
- `:documentation` - Documentation practices

## Customizing Your Taxonomy

When creating your taxonomy:

1. **Be specific to your domain** - Include project-specific components, patterns, and concerns
2. **Use consistent naming** - Follow kebab-case convention (`:multi-word-keyword`)
3. **Group related concepts** - Organize keywords into logical categories
4. **Document intent** - Explain what each keyword represents
5. **Evolve gradually** - Add keywords as concepts emerge, refactor when patterns become clear

## Keyword Composition

Keywords can be combined to express more specific concepts:

```markdown
[:security :authentication :api] - API authentication mechanisms
[:database :performance :caching] - Database performance via caching
[:architecture :modularity :separation-of-concerns] - Modular architecture design
```

## Validation

Use the `doc-keywords validate` command to ensure all keywords used in your documentation are defined in your taxonomy.
