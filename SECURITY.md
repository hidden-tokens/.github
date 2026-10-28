# Security Policy

Hidden Tokens exists so that sensitive information stays out of shared places. Security reports are taken seriously.

## How to report a vulnerability

**Please do not open a public GitHub issue for a security problem.**

You can report privately in either of two ways:

1. **GitHub private vulnerability reporting** — use *Security → Report a
   vulnerability* on any Hidden Tokens repository. This is enabled on all org
   repositories and reaches the maintainers directly, privately.
2. **Email** — [info@hiddentokens.com](mailto:info@hiddentokens.com) with:

- The affected surface (desktop app, browser extension, or website)
- A description of the issue and how it could be exploited
- Steps to reproduce, using fictional data only
- Any relevant version information

When a report is confirmed, we will acknowledge it, work on a fix, and publish it in a release. Please allow a reasonable window for a response — reports are reviewed promptly, but we are a small team.

**Important:** do not include real confidential data, credentials, or token↔value mappings in your report, even though it is sent privately. Fictional examples are enough to demonstrate any issue.

## Scope

- **Desktop app** — tokenization, mapping storage, and restoration behavior
- **Browser extension** — content handling and permissions
- **Website** ([hiddentokens.com](https://hiddentokens.com/)) — pages served to visitors

Out of scope:

- Reports about third-party AI services you choose to send content to — the tool's purpose is to let you control what leaves your machine; review your provider's policies separately
- Missing hardening without a demonstrated impact
- Social engineering against our support channels

## Supported versions

Security fixes target the **latest stable release** of the desktop app and the browser extension. If you are on an older version, update before reporting.

## What we will never ask for

We will never ask you to send a confidential document, credentials, or your mapping files as part of a security report or support conversation.
