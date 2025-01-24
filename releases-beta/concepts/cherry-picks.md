---
description: >-
  Read about the cherry-picks feature in Releases. Aviator enables teams to
  cherry-pick the same commit or pull request to multiple releases in one
  action.
---

# Cherry-picks

Cherry-picks are useful for hot fixes where a bug has been identified in a released version, but the mainline has moved much further along so cutting a new release may require a longer validation cycle. In such cases, a small localized fix can be “cherry-picked” on top of an existing release without cutting a new release from mainline.

Although the git CLI supports a simple cherry-pick action, doing manual cherry-picks can be prone to errors. For instance:

* accidentally cherrypick on top of a wrong branch
* accidentally deploy a wrong branch after cherrypicking
* forget to cherrypick to all environments / release candidates
* accidentally deploy the next release that does not contain that cherrypick after pushing the cherrypick (so rollback the fix)

Aviator Releases standardizes the process of cherry-picking while also enabling teams to cherry-pick the same commit or pull request to multiple releases in a single action.

Aviator Releases also ensures that once a cherry-pick is created, that any subsequent releases and deployments cannot be created without that cherry-picked commit avoiding any human errors.
