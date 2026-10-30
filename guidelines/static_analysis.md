# Guidance about static code analysis

## Static code analysis is good

Use static analyzers to catch issues early and consistently. Automated enforcement is generally less costly than relying on reviewers to spot every instance.

## Keep findings actionable

For deterministic checks intended to gate changes, treat warnings as errors. That is, fix or exempt violations, rather than leaving them alive. If possible, write your wrappers or configure your tooling to automatically treat as errors any warnings emitted by static code analyzers. The purpose of this is to prevent "noise" from accreting in tool output, because it can drown-out any important diagnostics that may arise.

## Handle exemptions deliberately

- Add a justification in a comment, wherever a rule is suppressed or a finding is exempted.
- Review exemptions critically; remove them when they are no longer needed.
- Prefer enabling rules broadly and suppressing them narrowly.
- When test-enabling a rule reveals existing violations, consider exempting them rather than leaving the rule disabled everywhere.

## Tighten analysis incrementally

Look for opportunities to enable useful checks in new or existing code. Apply stricter settings to new code where practical, and expand coverage incrementally without obscuring meaningful diagnostics.

## Verify compatibility

When multiple analyzers or compilers consume shared configuration, verify that each accepts the options and interprets them as intended. Similar option names do not guarantee identical support or diagnostics.
