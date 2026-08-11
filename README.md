# control-in-code

**Clean-code changes that move a compliance control.** An open dataset of the pull-request patterns that are neither bugs nor vulnerabilities, and still change what a company must answer for under SOC 2, ISO 27001, GDPR, and other frameworks.

This is the knowledge base behind [heygrc, compliance review for pull requests](https://heygrc.com). It is published openly because the category is bigger than any product: if you build review tooling, teach secure development, or prepare teams for audits, these patterns are yours to use with attribution (CC BY 4.0).


## Why this exists (short)

A pull request can look totally fine (tests pass, no obvious security issue) and still be the kind of change that shows up later in a SOC 2 or GDPR discussion.

Boring examples:

- remove an "admin only" check on delete because auth already runs
- delete a log line that records role changes because it is noisy
- add a column for email/phone with no plan for how long you keep it

None of those has to be a bug. They still matter for access control, audit logs, and personal data retention.

This repo is a free collection of those patterns (diffs + short notes). CC BY 4.0.

Optional product that comments on similar issues on real PRs: [heygrc](https://heygrc.com) (does not block merges). Live demo reviews: [heygrc-demo](https://github.com/better-isms/heygrc-demo/pulls).

---

## Why this dataset exists

Code review already answers two questions about a diff: is it correct, and is it safe. There is a third question that rarely has an owner: does this change touch a control the company is audited on. The defining property of that question is that **clean, working, secure code can still move a control**. A widened IAM role, a trimmed audit log, a new store of personal data with no retention bound: none of these has to be a defect, and each one changes the company's compliance posture.

Every entry here is that shape: a minimal, realistic diff, the framework control it touches (identifier + original plain-English gloss, never normative text), and why. All examples are synthetic and illustrative.

See the live version of these patterns reviewed by heygrc on real pull requests: [better-isms/heygrc-demo](https://github.com/better-isms/heygrc-demo/pulls).

## The change patterns (`data/patterns.json`)

9 common change patterns, each mapped to the controls it commonly implicates across frameworks:

| Change pattern | Controls it commonly implicates |
|---|---|
| Log a request body that contains personal data | GDPR Art. 5(1)(c); GDPR Art. 32; PCI DSS Req 3 |
| Drop a TLS floor or disable certificate verification | SOC 2 CC6.7; NIST 800-53 SC-8; PCI DSS Req 4; HIPAA 164.312(e)(1); ISO 27001:2022 A.8.24 |
| Widen an IAM role or drop an authorization check | SOC 2 CC6.1; ISO 27001:2022 A.8.3; NIST 800-53 AC-6; PCI DSS Req 7; HIPAA 164.312(a)(1) |
| Remove a security-relevant log or alert | ISO 27001:2022 A.8.15; SOC 2 CC7.2; NIST 800-53 AU-12; PCI DSS Req 10; HIPAA 164.312(b); EU AI Act Art. 12 |
| Add a personal-data store with no retention or deletion path | GDPR Art. 5(1)(e); GDPR Art. 17 |
| Skip a multi-factor check on a sensitive path | NIS 2 Art. 21(2)(j); NIST 800-53 IA-2; SOC 2 CC6.1; PCI DSS Req 8 |
| Remove a backup, retry, or failover path | DORA Art. 12; DORA Art. 11; ISO 27001:2022 A.8.13 |
| Add a third-party dependency with no integrity check | NIS 2 Art. 21(2)(d); PCI DSS Req 6 |
| Remove input validation or disable a security lint | ISO 27001:2022 A.8.28; NIST 800-53 SI-10; PCI DSS Req 6; NIS 2 Art. 21(2)(e) |

## The control deep-dives (`data/controls.json`)

25 controls, each with the failure shapes it takes in a diff and a worked example:

| Framework | Control | The shapes it takes in a diff |
|---|---|---|
| SOC 2 | CC6.1 | An access policy widens; An authorization check is dropped; A data grant broadens; A default flips to allow; A privilege-escalation path opens |
| SOC 2 | CC6.6 | A firewall or security group opens to the world; A managed resource is made publicly reachable; An admin or management port is exposed; An allowlist widens; A boundary control is bypassed |
| SOC 2 | CC6.7 | A TLS floor drops; An internal hop goes plaintext; Certificate verification is disabled; A new export sends data out; A protected field becomes loggable |
| SOC 2 | CC7.2 | An alert rule is deleted; A threshold is loosened; A metric stops being emitted; Coverage does not follow new surface; A detection is disabled |
| SOC 2 | CC8.1 | A required approval is removed; A migration skips review; Tests stop gating the merge; A bypass path is added; The rollback is removed |
| ISO 27001 | A.8.15 | A security log is removed; A log level drops below production; The actor is dropped from the record; A new privileged action ships unlogged; Retention is shortened |
| ISO 27001 | A.8.24 | A key lands in code; A weak primitive is introduced; A TLS floor drops; Verification is disabled; Key rotation or scope is loosened |
| ISO 27001 | A.8.28 | A query is built by string concatenation; Output is rendered unescaped; A security lint or check is disabled; Untrusted input is deserialised; Validation is removed |
| ISO 27001 | A.8.31 | A test config points at production; Production secrets land in a lower environment; A guard against cross-environment access is removed; Fixtures or seed data get a path to production; Environments share a backing store |
| GDPR | Art. 5(1)(e) | A new store ships with no end of life; A retention job is removed or disabled; A window quietly extends; A soft delete keeps everything; Erasure does not reach the new copy |
| GDPR | Art. 17 | A new store is not wired into deletion; A delete becomes a soft delete; Deletion is partial; A processor is never told to delete; Erasure is not actually reachable |
| GDPR | Art. 5(1)(c) | A payload widens to the whole object; A new field captures more than needed; A log line carries personal data; A third party receives more than necessary; Free-text is sent unredacted |
| GDPR | Art. 25 | A visibility default ships open; An opt-out where it should be opt-in; A broad scope is the default selection; A privacy control is added but defaults off; A safer default is loosened to help adoption |
| EU AI Act | Art. 12 | Inference logging is removed; Key fields are dropped from the record; A new high-risk path ships unlogged; Logging stops covering the system's operation; Log integrity is weakened |
| EU AI Act | Art. 10 | A dataset validation step is removed; A bias or representativeness check is skipped; Provenance is dropped; A new data source is added ungoverned; Filtering of bad records is removed |
| DORA | Art. 11 | A circuit breaker is removed; A retry or backoff is removed; A failover or redundancy path is removed; Graceful degradation is removed; A timeout is removed |
| DORA | Art. 9 | Segmentation between zones is flattened; A protective deny rule is widened; A critical function becomes broadly reachable; Egress controls are dropped; An isolation mechanism is removed |
| NIS 2 | Art. 21(2)(d) | A remote script is piped to a shell; A dependency is unpinned; An integrity check is removed; A new component arrives with no provenance; An internal mirror or allowlist is bypassed |
| NIS 2 | Art. 21(2)(g) | A dependency is held back from its fix; A base image is frozen and unmaintained; An automated update mechanism is disabled; A security patch is reverted; End-of-life software is kept in place |
| PCI DSS | Req 3 | Sensitive authentication data is stored; A PAN is stored without being unreadable; Account data lands in a new store; A masking or tokenisation step is removed; Card data is kept longer than needed |
| PCI DSS | Req 8 | MFA is removed or made optional; A shared or generic account appears; Strong auth is weakened; An auth step is bypassed; Default or static credentials are left in |
| HIPAA | 164.312(a)(2)(iv) | A new ePHI store has no encryption; An encryption config is removed; ePHI lands somewhere unprotected; A backup or export is unencrypted; Key handling makes the encryption ineffective |
| HIPAA | 164.312(c)(1) | A record integrity check is removed; A destructive write replaces a safe update; A guard against improper deletion is removed; Write validation is dropped; A record store becomes freely editable |
| NIST 800-53 | CM-7 | An unnecessary service is added; A management or debug interface is exposed; A disabled port or protocol is reopened; A development-only feature is left on; A broad default is left in place |
| NIST 800-53 | SI-10 | A file path is built from unvalidated input; A URL or host comes from unvalidated input; A validation check is removed; Bounds or length checks are removed; An allowlist is replaced by accepting anything |

## Three worked examples

### SOC 2 CC6.1: A refactor that drops an authorization check.

A route is being tidied up. The role check in the middle looks redundant next to the auth middleware, so it goes. The endpoint still requires a logged-in user, but it no longer requires the right one: now any authenticated user can delete any project.

```diff
# routes/projects.ts
- router.delete("/projects/:id", requireRole("admin"),
-   loadProject, deleteProject)
+ router.delete("/projects/:id", loadProject, deleteProject)
```

**SOC 2 CC6.1.** This removes the role check from a destructive endpoint. The auth middleware confirms the caller is signed in, but CC6.1 is about whether they are authorized for this action, and deleting any project is not something every user should be able to do. Restore the authorization check (a role or ownership check on the project) before the handler.

### ISO 27001 A.8.15: Auth failures dropped below the production log level.

Authentication-failure logs are noisy in development, so a change moves them from a warning to a debug level. In production the log level is set to info, so those failures now vanish from the record entirely, exactly the events you most want when investigating an intrusion.

```diff
# auth/login.ts
  if (!valid) {
-  logger.warn("auth.failed", { userId, ip })
+  logger.debug("auth.failed", { userId, ip })
    return unauthorized()
  }
```

**ISO 27001:2022 A.8.15.** With production at info level, dropping auth failures to debug means they are no longer recorded in production. A.8.15 expects security-relevant events, including failed authentication, to be logged and retained, and A.8.16 (monitoring) depends on them. Keep them at warning or route them to the security log rather than silencing them in production.

### GDPR Art. 5(1)(e): A removed purge that keeps inactive users forever.

A scheduled job that deleted the personal data of long-inactive users is dropped, maybe it was noisy, maybe it seemed safe to keep the data. The effect is that personal data now has no defined end of life.

```diff
# jobs/retention.yaml
  schedules:
    reconcile: { cron: "0 2 * * *" }
-  purge_inactive: { cron: "0 3 * * *", delete: users, older_than: P2Y }
```

**GDPR Art. 5(1)(e).** This removes the only thing that deleted personal data for inactive users, so it is now retained with no end date. Art. 5(1)(e) (storage limitation) expects personal data to be kept no longer than the purpose needs. If the job was too aggressive or noisy, fix its schedule or scope, but keep a retention path rather than removing it entirely.

## Format

- `data/patterns.json`: `{ id, label, blurb, implications: [{ framework, clause, note }] }`
- `data/controls.json`: `{ framework, frameworkName, id, title, intro, shapes: [{ label, detail }], example: { title, setup, file, delta, lines, clause, finding }, auditor, boundary }`

Identifiers and short public labels reference public framework numbering; the explanatory text is original plain English, and no normative framework text is reproduced. Nothing here is legal advice or a compliance determination: which obligations apply depends on the frameworks a company actually holds.

## License and attribution

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Attribute "heygrc (heygrc.com)". Corrections welcome: if an identifier is wrong or a gloss is imprecise, open an issue; we would rather fix it than defend it.

Maintained by [ISMS Copilot](https://ismscopilot.com), the team behind heygrc, compliance review for pull requests.

## Related

- Live reviews of these shapes: [better-isms/heygrc-demo](https://github.com/better-isms/heygrc-demo/pulls)
- Product: [heygrc.com](https://heygrc.com) (compliance review for pull requests)
- Free starter tool: generate a repo-root [`.heygrc.md`](https://heygrc.com/explore) for company context on reviews (browser-only, no account)

