## 2025-05-20 - Build Script I/O Bottleneck
**Learning:** The repository build scripts (`update-readme.mjs` and `generate-marketplace.mjs`) were performing redundant file I/O and YAML parsing for the same files (350+ skills and 190+ instructions). Implementing a central in-memory cache in `yaml-parser.mjs` for both raw file content and parsed frontmatter reduces build time measurably.
**Action:** Always check for redundant file operations in metadata extraction scripts. Use `readFileCached` and `parseFrontmatter` (which is now cached) instead of direct `fs.readFileSync` calls.
