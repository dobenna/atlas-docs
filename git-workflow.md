# Git Workflow Standard

## Purpose

This document defines the Git workflow used by Project Atlas.

The goal is to keep changes traceable, reviewable and aligned with professional Platform Engineering practices.

---

## Branch Strategy

Project Atlas uses the following branches:

- main
- develop
- feature/*
- hotfix/*

---

## Branch Naming

Examples:

feature/add-platform-architecture

feature/create-git-standards

feature/setup-jenkins-pipeline

hotfix/fix-readme-typo

---

## Commit Convention

Project Atlas uses Conventional Commits.

Format:

type(scope): description

Examples:

docs(standards): add git workflow standard

feat(platform): add initial terraform structure

fix(docs): correct architecture typo

ci(pipelines): add Jenkinsfile skeleton

---

## Common Commit Types

- docs
- feat
- fix
- chore
- ci
- refactor
- security

---

## Workflow

1. Checkout develop
2. Create feature branch
3. Implement changes
4. Commit
5. Push
6. Open Pull Request
7. Merge into develop
8. Promote to main when stable

---

## Rules

- Never work directly on main.
- Never commit secrets.
- Keep commits small.
- Document architectural decisions.
- Everything as Code.




