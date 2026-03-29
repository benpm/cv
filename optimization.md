# Resume ATS/AI Optimization Report

## Scope and goal
The goal of this optimization pass was to improve machine readability for ATS and AI-based resume parsers while preserving a professional layout for human readers.

Primary target file:
- benpm_cv.tex

Primary outcome target:
- Better extraction of contact info, section labels, dates, company/title pairs, and numeric achievement statements when converting PDF to plain text.

## Source-backed principles used
I used the following external guidance as the decision baseline:

1. Use simple formatting and clear headings; avoid parser-hostile layouts.
2. Avoid headers/footers, tables, columns, and graphics for machine parsing reliability.
3. Keep key information in plain, linear text and test with plain-text extraction.
4. In LaTeX PDFs, improve glyph-to-Unicode mapping to reduce copy/paste and parser corruption.

These principles are supported by sources [1]-[6] listed at the end.

## Process followed

### 1) Baseline measurement before edits
I compiled the existing PDF and extracted text using pdftotext to observe real parser output before changing anything.

Observed problems in baseline extraction:
- Contact line included a decorative star symbol that was not semantically useful for parsing.
- Company names were coupled with "@" formatting, which is visually fine but less parser-friendly than clean role/company separation.
- Math-mode notation in prose degraded extraction quality for numeric claims (for example, 2^25 and 2^44 effectively collapsed in plain-text extraction).
- Date strings were inconsistent (for example, seasonal date wording).

Why this step is justified:
- TopResume explicitly recommends converting to plain text to see whether information is missing, misordered, or garbled [3].

### 2) Encoding and PDF text mapping hardening
Changes made:
- Replaced deprecated utf8x input encoding with utf8.
- Added:
  - \input{glyphtounicode}
  - \pdfgentounicode=1

Why this is justified:
- LaTeX machine-readability discussions and examples repeatedly use glyphtounicode + pdfgentounicode to improve copy/paste and parser extraction [5][6].

Expected effect:
- Better Unicode mapping from glyphs in generated PDF text.
- Fewer parser errors caused by font/glyph translation issues.

### 3) Removed parser-hostile decorative structures
Changes made:
- Removed boxed date rendering and decorative wrappers around machine-critical text.
- Simplified entry macros so dates, titles, and organizations appear in straightforward linear text.
- Removed unused table/box-driven constructs that were not needed for a single-column ATS-friendly output.

Why this is justified:
- Indeed and TopResume both emphasize simple layouts and avoiding tables/graphics/complex formatting that ATS tools may parse poorly [1][2][3][4].

Expected effect:
- Improved extraction order and reduced risk of dropped fields.

### 4) Header and contact normalization
Changes made:
- Simplified name/contact macro usage to plain text.
- Replaced decorative separator symbol with a plain text separator.
- Kept contact details in the document body (not header/footer metadata).

Why this is justified:
- ATS guidance recommends keeping contact details in easy-to-read body text and avoiding structures that can be skipped [2][3][4].

Expected effect:
- More reliable extraction of email and portfolio URL.

### 5) Semantic section and date normalization
Changes made:
- Renamed "Professional Experience" to "Work Experience".
- Added a concise "Summary" section with strong keyword content.
- Standardized dates to a consistent month-year style.
- Converted seasonal date wording to explicit month wording where possible.

Why this is justified:
- ATS systems rely on standard section labels such as Work Experience, Education, and Skills [1][2].
- Date formatting consistency is a commonly evaluated ATS readability criterion [4].
- A summary/headline and keyword distribution are recommended for ATS matching [1][3].

Expected effect:
- Better section classification and timeline parsing.
- Better keyword recall without keyword stuffing.

### 6) Replaced math-mode prose and special-symbol dependence
Changes made:
- Replaced prose math symbols (for example arrow notation) with plain-language equivalents.
- Rewrote compact exponent claims into plain numeric wording when needed to avoid text-mode compile/parsing problems.

Why this is justified:
- ATS advice consistently warns that special characters and non-standard formatting can reduce parse quality [2][3].
- LaTeX symbol extraction issues are well documented when glyph mappings are imperfect [5][6].

Expected effect:
- More reliable extraction of metrics and achievement statements.

## Validation performed after edits

### Build validation
- Recompiled PDF after each significant patch to ensure no blocking LaTeX errors.

### Extraction validation
- Re-ran pdftotext and compared before/after snapshots.

Key before/after improvements observed:
- Contact extraction became plain and explicit (email + URL on one clean line).
- "@ Company" style coupling was removed in favor of separate role/company lines.
- Seasonal date phrase was normalized to explicit month-year style.
- Numeric claim wording now survives plain-text extraction in human-readable form.

Why this validation is justified:
- Plain-text extraction testing is directly aligned with ATS readability recommendations [3][4].

## Trade-offs and remaining issues
- The visual style was kept mostly intact, but machine readability was prioritized where conflicts existed.
- A pre-existing KOMA/sectsty compatibility warning remains from the original template stack; it does not block output but could be cleaned in a future pass.
- PDF remains the output artifact here. Some ATS portals still parse DOCX more reliably; portal-specific file-format choice is still important [1][3].

## Practical recommendations for next iterations
1. Run at least one live ATS parser/upload test against the current PDF and compare field extraction.
2. Maintain a DOCX fallback for strict portals that perform better with Word documents.
3. If needed, remove sectsty and migrate section styling to KOMA-native controls for cleaner LaTeX warnings.
4. Re-run plain-text extraction after every content update to catch regressions early.

## References
[1] Indeed - How To Write an ATS Resume (With Template and Tips):
https://www.indeed.com/career-advice/resumes-cover-letters/ats-resume-template

[2] Indeed - How To Create an Applicant Tracking System (ATS) Resume:
https://www.indeed.com/career-advice/resumes-cover-letters/applicant-tracking-system-resume

[3] TopResume - How to Make an ATS-Friendly Resume:
https://topresume.com/career-advice/what-is-an-ats-resume

[4] TopResume - How to Check if My Resume is ATS-Friendly for Free:
https://topresume.com/career-advice/ask-amanda-resume-ats-readability

[5] TeX StackExchange - How to improve machine-readability of a CV created in LaTeX with moderncv:
https://tex.stackexchange.com/questions/439907/how-to-improve-machine-readability-of-a-cv-created-in-latex-with-moderncv

[6] TeX StackExchange - How to fix missing or incorrect mappings from glyphtounicode.tex:
https://tex.stackexchange.com/questions/66300/how-to-fix-missing-or-incorrect-mappings-from-glyphtounicode-tex
