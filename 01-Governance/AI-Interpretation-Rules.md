# AI Interpretation Rules

These rules govern how an AI agent or investigator should read this playbook.

1. **Observed is not Runtime Confirmed.** UI presence does not prove execution semantics.
2. **Documented is not Runtime Confirmed.** Product guidance describes intended or documented behavior, not necessarily every runtime edge case.
3. **Runtime Confirmed requires evidence.** A reproducible test and recorded result are required.
4. **Open means unknown, not unsupported.** Do not turn an unanswered question into a negative claim.
5. **Canonical references explain current behavior.** Evidence files prove it.
6. **Verification contains active questions only.** Resolved questions leave the verification layer.
7. **Never invent parameters, outputs, expressions, limits, or error behavior.** Mark them Open until established.
8. **Prefer deterministic primitives for deterministic work.** Use Agent reasoning where semantic interpretation is actually required.
9. **Treat variable scope explicitly.** Do not assume two similarly named variables are interchangeable.
10. **Historical failures remain evidence.** A corrected workflow does not erase what the earlier failure taught us.
11. **Use the newest established evidence when sources conflict.** Record the contradiction and update the canonical reference.
12. **Never expose secrets.** Credentials, tokens, authorization values, and private keys are prohibited from examples and repository content.
