# Git

> *Version control essentials*

---

## Why Git?

- **History** - Track all changes
- **Collaboration** - Work with teams
- **Branching** - Parallel development
- **Backup** - Remote repositories

---

## Basic Workflow

```bash
# Clone repository
git clone https://github.com/user/repo.git

# Create branch
git checkout -b feature/new-feature

# Make changes and stage
git add .

# Commit
git commit -m "feat: add new feature"

# Push
git push origin feature/new-feature
```

---

## Common Commands

```bash
# Status
git status

# Log
git log --oneline -10

# Diff
git diff

# Stash
git stash
git stash pop

# Reset
git reset --hard HEAD~1

# Merge
git checkout main
git merge feature/new-feature

# Rebase
git rebase main
```

---

## Branching Strategy

```
main (production)
  │
  └── develop
        │
        ├── feature/user-auth
        ├── feature/api-v2
        └── bugfix/login-issue
```

---

## Commit Messages

Use [Conventional Commits](https://conventionalcommits.org):

```
feat: add user authentication
fix: resolve login redirect bug
docs: update API documentation
refactor: simplify database queries
test: add unit tests for auth
chore: update dependencies
```

---

*Back to: [[Index|Tools Home]]*
