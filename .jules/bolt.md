## 2025-05-15 - Caching redundant file reads in build scripts
**Learning:** The build process for `awesome-copilot` involves processing over 1,500 markdown files, often reading and parsing the same file multiple times (e.g., for title extraction and then for frontmatter/description extraction). Adding a simple process-level in-memory cache for file contents and parsed frontmatter significantly reduces I/O and CPU overhead.
**Action:** Use `readFileCached` and the cached `parseFrontmatter` in `eng/yaml-parser.mjs` for any scripts that process the same files repeatedly during a single execution.

## 2025-05-15 - Prefer native string methods over regex for simple trimming
**Learning:** Using regular expressions like `/[\r\n]+$/g` or `/[\s\r\n]+$/g` for simple trailing whitespace removal can trigger SonarCloud "Security Hotspots" due to potential Regular Expression Denial of Service (ReDoS) vulnerabilities. Native methods like `trim()` and `trimEnd()` are safer, more performant, and more readable.
**Action:** Use `string.trim()` or `string.trimEnd()` instead of regex for cleaning up parsed frontmatter fields.
