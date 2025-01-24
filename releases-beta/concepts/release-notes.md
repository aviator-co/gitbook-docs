---
description: >-
  Read the concepts behind release notes from Aviator Releases. Helpful for
  internal teams, QA, support, for a shared understanding of recent code
  changes.
---

# Release notes

Defining Release notes or Changelog is a common practice for open-source software or a software that is typically shared as pre-built binaries to external users.

But release notes can also be very helpful for internal teams:

* Facilitates collaboration between development, QA, support, and other teams by providing a shared understanding of what has been modified.
* Helps onboarding and training by providing a clear history of enhancements to the product.
* Aids in troubleshooting issues by providing context about recent updates that may have introduced new bugs or resolved existing ones.
* Serves as a valuable reference for maintaining internal documentation and user manuals.
* Enables internal teams to provide informed feedback on new features and changes, contributing to continuous improvement.
* Helps ensure compliance requirements are met by documenting changes systematically.
* Assists in future planning and development by reviewing past changes and understanding their impact.
* Equips support teams with the necessary information to address customer inquiries about new features or fixes.
* Tracks the performance of development teams by documenting deliverables and milestones.

## Compared to GitHub

GitHub also natively offers Releases that can generate Release notes. But these release notes can get messy when using a monorepo where multiple micro-services released from a single repository. Since GitHub’s Releases are linear with no separation of micro-services, the release notes are generated as a diff from the last release that may or may not be tied to the same service.

Additionally, the default release notes generated from GitHub do not account for different code paths or dependencies to understand what changes are associated with which micro services.

Using Aviator Releases, you can manage separate release notes and changelog history for each service even within the same repository. Moreover, using separate release fag formats and declarative code paths, the release notes can be uniquely generated for each service.
