# 🤖 Command: pr-description

> Generates a pull request description from branch changes.

## Instructions for Claude

1. Read branch diff: `git log main..HEAD --oneline` + `git diff main --stat`
2. Generate PR description:

```markdown
## What

<!-- 1-2 sentences: what does this PR do? -->

## Why

<!-- Link to task/issue. Why is this change needed? -->

## How

<!-- Brief technical summary of the approach -->

## Changes

<!-- Key files changed and what was done -->

## Testing

<!-- How was this tested? What should the reviewer check? -->
- [ ] Unit tests pass
- [ ] Manual testing done
- [ ] [specific scenario to verify]

## Screenshots

<!-- If UI changed, add before/after screenshots -->
```

3. Copy to clipboard or output for the user
