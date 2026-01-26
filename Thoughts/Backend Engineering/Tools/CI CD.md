# CI/CD

> *Automate testing and deployment*

---

## What is CI/CD?

| Term | Meaning |
|------|---------|
| **CI** | Continuous Integration - Automated testing |
| **CD** | Continuous Delivery/Deployment - Automated releases |

---

## Pipeline Stages

```
Code Push → Build → Test → Deploy (Staging) → Deploy (Prod)
```

---

## GitHub Actions

```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: pip install -r requirements.txt
      
      - name: Run tests
        run: pytest
  
  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: |
          # Deploy commands here
```

---

## GitLab CI

```yaml
# .gitlab-ci.yml
stages:
  - test
  - build
  - deploy

test:
  stage: test
  script:
    - pip install -r requirements.txt
    - pytest

build:
  stage: build
  script:
    - docker build -t myapp .
    - docker push registry.example.com/myapp

deploy:
  stage: deploy
  script:
    - kubectl apply -f k8s/
  only:
    - main
```

---

## Best Practices

1. **Fast feedback** - Keep builds under 10 min
2. **Test everything** - Unit, integration, e2e
3. **Fail fast** - Stop on first failure
4. **Environment parity** - Match prod closely
5. **Rollback plan** - Easy reverts

---

*Back to: [[Index|Tools Home]]*
