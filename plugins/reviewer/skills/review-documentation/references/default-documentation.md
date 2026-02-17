# Default Documentation Review Rules

## Accuracy

- **Code references** - file paths, function names, class names, and command examples match what actually exists in the codebase
- **API documentation** - parameter names, types, return values, and behavior descriptions match the implementation
- **Installation instructions** - commands work, dependencies are listed, prerequisites are stated
- **Configuration examples** - config keys, value types, and defaults match the actual configuration schema
- **Version references** - version numbers, compatibility claims, and deprecation notices are current

## Completeness

- **Public API coverage** - all public functions, types, and endpoints have documentation
- **Getting started path** - new users can go from zero to working setup by following the docs
- **Error documentation** - common errors, their causes, and solutions are documented
- **Environment requirements** - required tools, versions, and system dependencies are stated
- **Migration guides** - breaking changes between versions have upgrade instructions

## Structure

- **Logical organization** - content flows from overview to details (progressive disclosure)
- **Consistent headings** - heading levels are used correctly (no skipped levels, consistent style)
- **Table of contents** - long documents (>200 lines) have a TOC or clear navigation
- **Cross-references** - related docs link to each other; no orphaned pages
- **Section completeness** - sections have content, not just headings (no empty placeholder sections)

## Quality

- **Conciseness** - documentation is as short as possible while remaining clear; no unnecessary repetition
- **Examples** - non-trivial features include usage examples with expected output
- **Formatting** - code blocks use correct language tags, tables are well-formatted, lists are consistent
- **Tone consistency** - consistent voice (imperative for instructions, declarative for descriptions)
- **Link integrity** - all links (internal and external) point to valid targets

## Staleness Indicators

- **Stale references** - mentions of files, features, or configurations that no longer exist
- **TODO/FIXME in docs** - unresolved placeholders left in published documentation
- **Date references** - absolute dates that may be outdated
- **Screenshot/diagram drift** - visual content that no longer matches the current UI or architecture
