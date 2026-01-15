# Internationalization (i18n)

## Overview

The MCP Server Security Standard (MSSS) is designed to be translated into multiple languages to support the global security community. This document describes the translation process and guidelines.

## Authoritative Language

**English is the authoritative language** for all MSSS documents. In case of conflicts, ambiguities, or discrepancies between translations, the English version shall govern.

## Supported Languages

Current status is tracked in `languages.json`:

- **Authoritative**: English (en)
- **Supported**: English only (as of v0.1)
- **Planned**: Spanish, Portuguese (BR), French, German, Chinese (Simplified), Japanese

## Translation Guidelines

### 1. What to Translate

The following documents SHOULD be translated:

- `/README.md`
- `/CHANGELOG.md`
- `/standard/msss.md`
- `/standard/profiles.md`
- `/standard/threat-model.md`
- All controls in `/controls/`
- `/governance/contributing.md`
- This file (`/i18n/README.md`)

The following documents MAY remain English-only:

- JSON schemas (technical, language-neutral)
- `/research/notes.md` (internal documentation)

### 2. Directory Structure

Translations SHALL use the following structure:

```
/standard/
  msss.md              (English - authoritative)
  es/
    msss.md            (Spanish translation)
  pt-BR/
    msss.md            (Portuguese translation)
  
/controls/
  MCP-FS-01-path-allowlisting.md       (English)
  es/
    MCP-FS-01-path-allowlisting.md     (Spanish)
```

### 3. Translation Process

1. **Claim a document**: Open an issue to indicate you're translating a specific document to avoid duplicate work
2. **Translate**: Maintain the same structure, headings, and formatting as the English version
3. **Preserve technical terms**: Some terms should NOT be translated:
   - Control IDs (e.g., `MCP-FS-01`)
   - Status enums (`PASS`, `FAIL`, `NOT_APPLICABLE`, `MANUAL_REVIEW`)
   - Level designations (`L1`, `L2`, `L3`)
   - Domain codes (`FS`, `EXEC`, `NET`, etc.)
   - RFC 2119 keywords (`MUST`, `SHOULD`, `MAY` - translate but keep reference to RFC 2119)
4. **Add attribution**: Include translator credit at the bottom:
   ```markdown
   ---
   **Translation**: [Your Name] (@github_username)  
   **Date**: YYYY-MM-DD  
   **Based on**: English v0.1.0
   ```
5. **Submit PR**: Follow contribution guidelines in `/governance/contributing.md`

### 4. Maintaining Translations

- Translations MUST track the English version they're based on (e.g., "Based on: English v0.1.0")
- When the English version is updated, translation maintainers SHOULD be notified
- Outdated translations MUST include a warning banner indicating they're not current

Example banner:
```markdown
> ⚠️ **Translation Notice**: This translation is based on English v0.1.0. The authoritative version has been updated to v0.2.0. Some content may be outdated.
```

### 5. Quality Standards

Translations MUST:
- Be technically accurate
- Use consistent terminology (create a glossary for your language)
- Maintain the same level of formality as the English original
- Preserve all links and references
- Not alter the meaning or requirements of controls

Translations SHOULD:
- Be reviewed by at least one other fluent speaker
- Follow local conventions (e.g., date formats, decimal separators)
- Adapt examples to be culturally appropriate where necessary

### 6. Glossary

Each language translation SHOULD maintain a glossary file mapping English technical terms to the target language:

`/i18n/{lang}/glossary.md`:

```markdown
# Glossary: English → Spanish

| English | Spanish | Notes |
|---------|---------|-------|
| MCP server | Servidor MCP | Do not translate "MCP" |
| Tool | Herramienta | Technical term |
| Resource | Recurso | |
| Prompt | Prompt | Keep in English or use "Instrucción" |
```

### 7. RFC 2119 Keywords

RFC 2119 keywords (`MUST`, `SHOULD`, `MAY`) have specific legal meanings. Translations SHOULD:

1. Translate the keyword for readability
2. Keep the English term in parentheses for clarity
3. Reference RFC 2119 in an introductory note

Example (Spanish):
```markdown
## Lenguaje Normativo

Este estándar utiliza las siguientes palabras clave según se definen en RFC 2119:

| Término | Significado |
|---------|-------------|
| **DEBE (MUST)** | Requisito absoluto. El incumplimiento resulta en FALLO. |
| **DEBERÍA (SHOULD)** | Recomendado. La desviación requiere justificación documentada. |
| **PUEDE (MAY)** | Opcional. Sin impacto en el cumplimiento si se omite. |
```

### 8. Version Synchronization

- Translation version numbers SHOULD match the English version they're based on
- If a translation lags behind (e.g., English is v0.2 but Spanish is still based on v0.1), clearly mark this in the document header

### 9. Community Coordination

- Translation discussions happen in GitHub Discussions under the "i18n" category
- Language-specific channels MAY be created for larger translation communities
- Quarterly sync meetings for active translators (announced in Discussions)

## Volunteer

We welcome translation volunteers! To contribute:

1. Check `languages.json` for priority languages
2. Open an issue: "Translation: [Language] - [Document]"
3. Fork the repository and create your translation
4. Submit a pull request

See `/governance/contributing.md` for detailed contribution guidelines.

## Contact

For translation questions:
- Open an issue with the `translation` label
- Join GitHub Discussions → i18n category

---

**Version**: 0.1.0  
**Last Updated**: 2026-01-15
