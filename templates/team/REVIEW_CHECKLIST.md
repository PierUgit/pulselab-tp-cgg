# Review checklist for AI-assisted changes

- [ ] I understand every changed line.
- [ ] Tests pass AND at least one test fails if I break the new code (checked).
- [ ] No input array is modified in place; units and dB convention are right.
- [ ] No new dependency, absolute path, secret or large file.
- [ ] Behaviour changes are described in the pull request; golden files changed only on purpose.
- [ ] The diff does not touch files outside the scope of the task.
- [ ] I read the test diff first: no expected value changed just to make a test pass.
