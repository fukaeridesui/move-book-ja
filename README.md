# The Move Book

This is the repository for [the Move Book](https://move-book.com) and
[Move Language Reference](https://move-book.com/reference).

## Japanese Translation Site Overview

For information on running the Japanese translation site for The Move Book and Move Language Reference, see [docs/overview.md](docs/overview.md). It covers the project purpose, technology stack, deployment to GitHub Pages, and ongoing translation workflow. Section 2-5 in that document provides a step-by-step checklist for deploying to GitHub Pages.
Section 2-6 explains how `main` merges trigger automatic GitHub Pages deployments, and Section 3-5 shows a GitHub Actions workflow to open weekly upstream-sync PRs.

## Structure

- Two books are placed in the `book` and `reference` directories. The `book` directory contains the
  main book, and the `reference` directory contains the reference book.
- The `packages` directory contains the code samples used in both books.
- The `site` directory contains [docusaurus](docusaurus.io) configuration and custom plugins for it.

## Running the Books Locally

### Prerequisites

- NodeJS
- `pnpm` (installation is: `npm i -g pnpm`)

### Local Server

> All commands can be run from the root.

```bash
pnpm start
```

_The book will be available at `http://localhost:3000`._

### Production Build Test

```bash
pnpm build
pnpm serve
```

## Archive

For the archive of the old version of the book, see the `archive` branch.
