Review the changes on @branch: 

- Think through how data flows in the app. Explain new patterns if the exist and why.
- Were there any changes that could affect infrastructure?
- Consider empty, loading, error, and offline states.
- Review frontend changes for WCAG 2.2 AA compliance (keyboard navigation, focus management, ARIA roles, color contrast).
- Review frontend changes for suitablitiy for desktop and tablet screen sizes (including iPad Mini and 4K screens)
- Did we add any unnecessary dependencies? If there is a heavy dependency, could we inline a more minimal version?
- Did we add quality tests? Prefer fewer, high quality test, Prefer integration tests for user flows.
- Were there schema changes which could require a database migration? 
- Changes to auth flows or permission? Run /security-review.
- If feature flags are set up, does this change require adding a new one?
- if i18n is set up, are the strings added localized and new routes internationalized?
- Are there places we should use caching?
- Are we missing critical o11y or logging on backend changes?