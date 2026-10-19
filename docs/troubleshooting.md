# Problems and bugs

## Status

No runtime bug has been reproduced in this repository yet. The original incomplete template had an unclosed Markdown code fence; this document replaces it with a complete template.

## Design review findings

| Finding | Treatment | Status |
| --- | --- | --- |
| Plan JSON described as an AST | Describe Terraform plan export accurately | Documentation corrected |
| Failed workflow assumed to block merge | Require final check in repository settings | Implementation pending |
| Insecure examples mixed with normal target | Separate examples/insecure | Layout prepared; tests pending |
| OPA false result mistaken for command failure | Require explicit true decision | Wrapper pending |
| Missing encryption called unencrypted storage | Distinguish SSE-KMS policy from S3 defaults | Documentation corrected |

## Runtime issue template

Copy this section when a real failure occurs.

### BUG-NNN — Short title

- Date / commit / environment:
- Status: open
- Impact:
- Reproduction command:
- Expected result:
- Actual result and exit code:
- Sanitized error output:
- Root cause: unknown until verified
- Fix attempted:
- Verification command and result:
- Regression coverage:
- Evidence or issue link:

Do not mark a bug fixed until the reproduction has been rerun.
