You are an AI coding assistant working in a multi-project repository. Perform a comprehensive, repeatable technical review across all projects and produce a single consolidated report.

Make sure you review all projects in the repository.

You are allowed to run the following commands yourself; you do not need to ask the user to do so:
- Hotspots (change frequency):
    - git log --since=12.months --name-only --pretty=format: | sort | uniq -c | sort -nr | cat
- Code ownership:
  - git shortlog -sne | cat
  - For 3-5 top hotspot files (from the first command), also run: git blame --line-porcelain <path> | sed -n 's/^author //p' | sort | uniq -c | sort -nr | head -20 | cat
- Churn (added/removed and unusually large commits):
  - git log --stat --since=12.months | cat
- Temporal coupling (files that change together):
  - PowerShell (no awk needed):
    - git log --since="12 months ago" --name-only --pretty=format:"__COMMIT__" | % { if ($_ -eq '__COMMIT__') { $prev = $null } else { $line = $_.Trim(); if ($line) { $line = $line.ToLower(); if ($prev) { "$prev`t$line" }; $prev = $line } } } | Group-Object | Sort-Object Count -Descending | Select-Object -First 200 | % { "$($_.Count)`t$($_.Name)" }
  - If you prefer WSL/awk: 
    - git log --since=12.months --name-only --pretty=format: | awk 'NF {print tolower($0)} !NF {prev=\"\"} NF && prev!=\"\" {print prev \"\t\" $0; prev=$0} NF && prev==\"\" {prev=$0}' | sort | uniq -c | sort -nr | head -200 | cat

Constraints and conventions:
- Do not alter any source code. Only create/edit markdown files in the repo root.
- Do not run/build/release the solution. Ask me to run any commands you need (e.g., git) and report results back.
- Do not commit changes. Ask me to commit.
- Favour ORM over raw SQL and stored procedures in findings/recs.
- Test guidance must prefer NSubstitute and Shouldly (not Moq/FluentAssertions).
- Web UI guidance must target WCAG 2.2 AA.
- When citing existing code, use CODE REFERENCES with required format:
  ```startLine:endLine:filepath
  // snippet
  ```
  Never add language tags or line numbers inside the code content.
- Preserve existing indentation styles in any code reference text.

Deliverables (create these files in the repo root):
1) Per-project reviews: For each project discovered via *.csproj in the solution, create TechnicalReview-<Project>.md containing:
   - Overview
   - Key Findings (Problems)
   - Recommendations (Solutions)
   - Prioritized Actions
   - Code references (exact line ranges, use CODE REFERENCES format)
   - Big brain files (largest risk/complexity files)
   - WCAG hotspots
   - Security risk hotspots

2) ArchitecturalReview.md with:
   - Executive summary
   - AWS architecture (S3/SQS/SES/etc.), Redis, logging
   - Database schema (SPs, temp tables, indexes, migrations stance)
   - Software architecture (layering, DI, logging consistency, async patterns)
   - Code references
   - Big brain files (architecture)
   - Action plan (phased milestones, owners, acceptance criteria)

3) MasterReview.md:
   - A single self-contained master report that inlines the full content of all the above reviews (no placeholders or links), with a brief table of contents at the top.

What to analyze and report on:
Socio-technical and evolution (request I run commands and paste output when needed):
- Hotspots (change frequency × complexity):
  - Ask me to run: git log --since=12.months --name-only --pretty=format: | sort | uniq -c | sort -nr
  - Combine change frequency with static complexity proxies (file length, number of methods, nested blocks) to identify top hotspots.
- Temporal coupling (files that change together):
  - Ask me to run: git log --since=12.months --name-only --pretty=format: | awk blocks to count co-changes (or provide a script) and paste results; flag strongly coupled pairs/modules.
- Code ownership and knowledge distribution (bus factor):
  - Ask me to run: git shortlog -sne to list contributors; git blame samples for hotspot files. Identify low-ownership/high-risk areas.
- Churn and delivery risk:
  - Ask me to run: git log --stat --since=12.months | summarize top files by added/removed lines and unusually large commits.

Static code health (proxy metrics inside the codebase):
- Excessive complexity/size:
  - Flag “god classes” (very large files/classes), long methods, high parameter counts, deep nesting/switch pyramids.
- Duplication and divergent forks:
  - Find near-identical helpers/utilities across projects; flag copy-paste risks.
- Architecture violations:
  - Data access in MVC/controllers, cross-layer references, missing interfaces in services, tight coupling to logging providers.
- Async pitfalls:
  - async void, .Wait(), .Result, GetAwaiter().GetResult(), missing cancellation tokens.
- Test gaps around hotspots:
  - For each hotspot, map to nearest test project; flag when complexity/change is high but tests are absent/thin.
- Security posture:
  - HSTS/HTTPS/CSP/cookies, antiforgery on POSTs, secrets in configs, S3 ACLs (PublicRead), missing presigned URLs or SSE-KMS.
- Performance and resiliency:
  - Missing retries/backoff/timeout policies for external calls (AWS/HTTP/DB), no DLQ/idempotency for SQS, synchronous I/O in hot paths.
- ORM alignment and DB health:
  - Stored procedures/temp tables on critical paths, lack of EF Core migrations, missing transactional boundaries.

Process:
- Produce a todo list so you don't run into a timeout problem.
- Enumerate solution (*.sln, *.csproj).
- Gather evolution signals (ask me to run git commands and paste results).
- Scan code for the static indicators above; then read exact files/lines to create authoritative code references.
- Author per-project TechnicalReview-*.md, ArchitecturalReview.md, then MasterReview.md that inlines all content.
- Ask me to review and commit.

At the end:
- List all created/updated files.
- Call out the top 10 highest risks (hotspots × complexity, temporal coupling clusters, low-ownership hotspots).
- Ask me to run any optional checks and to commit the markdowns.