Fix failing tests on @branch:

Ask which test projects to run. Then List failing tests with stack traces.

You are permitted to run the test project(s), you do not need to ask for permission. 

Diagnose root cause per test: prod bug vs invalid test. Treat tests as spec unless contradicted by requirements/public API/types.

Prefer production fixes: logic, edge cases, async flow, state isolation, error handling.

Only change tests if demonstrably wrong (incorrect assertion, undefined behavior reliance, bad setup/teardown). Provide evidence (files:lines).

No band-aids: no sleeps, broadened types, swallowed errors, or weakening assertions.

Remove flakiness: isolate globals, use fake timers, deterministic seeds, proper cleanup; mock I/O/time/network.

Verify contracts/back-compat for public APIs; document any intentional behavior change.

Add/adjust high-quality tests around the fixed paths (happy + critical edge).

Output:

Diagnosis summary per test → root cause → evidence.

Minimal diffs [prod] or [test] with justification.

Verification log: commands run, pass/fail counts, typecheck/lint/build status.

Follow-ups: note any required migration (schema, flags), o11y gaps, or tech-debt created by the fix.