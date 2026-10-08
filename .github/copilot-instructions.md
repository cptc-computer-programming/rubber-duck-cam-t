When reviewing pull requests:

- Focus on correctness, security, concurrency, and error handling.
- Flag changes that introduce unnecessary complexity.
- Check that public APIs have appropriate tests.
- Check for race conditions and resource leaks.
- Prefer existing project patterns over introducing new abstractions.
- Do not comment on formatting handled by automated tooling.
- Ignore documentation-only changes unless they contain formatting or grammar problems.
- Keep reviews terse. Minimize token usage, avoid repetition, and do not explain obvious issues at length.
