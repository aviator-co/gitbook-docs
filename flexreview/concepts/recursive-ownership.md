---
description: >-
  Learn how recursive ownership enhances code review workflows by assigning
  ownership dynamically in FlexReview.
---

# Recursive Ownership in FlexReview

GitHub defines [`CODEOWNERS` config](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners). However, its semantics is not expressive enough to capture the actual shape of code ownership. We define a new format to define ownership for the codebase.

## GitHub CODEOWNERS file

GitHub's CODEOWNERS file uses the last-matching rule. When one file matches with multiple lines in the file, the last one takes the precedence and other entries are not in effect. For example, imagine that you have following `CODEOWNERS`file.

```python
# CODEOWNERS
src             @acme-corp/engineering
src/ios         @acme-corp/ios-eng
src/ios/auth    @acme-corp/ios-auth-eng
src/ios/net     @acme-corp/ios-net-eng
```

If you modify files in `src/ios/auth`and `src/ios/net`, your PR needs an approval from two teams due to the last-matching rule. However, based on the file paths, it could have had a reviewer from `@ios-eng` because all the files are under `src/ios`.

The format allows you to put multiple teams on one line. By using this, you can put `@ios-eng` to all child directory entries to see if it can minimize the number of reviewers. This ends up in picking a reviewer from each team.

For this reason, we see that `CODEOWNERS` not flexible enough.

When using FlexReview with just the CODEOWNERS file, Aviator reviewer assignment should still work within the CODEOWNERS bounds. If there are multiple teams assigned on a single line, Aviator will only choose reviewers from the first team.

## Aviator OWNERS file and distributed aviator-config.yaml

{% hint style="info" %}
These are optional owner configurations that provide more capabilities for FlexReview.&#x20;
{% endhint %}

To address the inflexibility of the `CODEOWNERS`file, we introduce optional new files:  `.aviator/OWNERS` and `aviator-config.yaml`.

#### Aviator OWNERS file

This file uses the similar format as the GitHub equivalent, but you can put only one team per line. The file is interpreted differently from GitHub's. Given a file path, owners are defined as follows:

* **Owners**: All matching teams are considered as an owner of the file.
* **Direct Owners:** The owner with the longest matching path is called a direct owner.
* **Indirect Owners:** Non-direct owners are called indirect owners.

Take the same file as an example. Let's say we have this in `.aviator/OWNERS`file.

```python
# .aviator/OWNERS
src             @acme-corp/engineering
src/ios         @acme-corp/ios-eng
src/ios/auth    @acme-corp/ios-auth-eng
src/ios/net     @acme-corp/ios-net-eng
```

For a file under `src/ios/auth`, owners are defined as follows:

* **Owners:** `@engineering`, `@ios-eng`, `@ios-auth-eng`
* **Direct Owners:** `@ios-auth-eng`
* **Indirect Owners:** `@engineering`, `@ios-eng`

As you can see, FlexReview recognizes the file ownerships in a recursive way.

#### aviator-config.yaml

`aviator-config.yaml` is another format to configure ownership. This has a following structure:

```yaml
# src/ios/aviator-config.yaml
owners:
  # Each entry has a required "team" and an optional "paths".
  # If paths is omitted, it means that all files in the directly are recursively
  # owned by that team.
  # The paths are interpreted as a relative path to the directory the file is
  # contained in.
  - team: "acme-corp/ios-eng"  # This is interpreted as $REPO_ROOT/src/ios
  - team: "acme-corp/ios-auth-eng"
    paths:
      - "auth"  # This is interpreted as $REPO_ROOT/src/ios/auth 
  - team: "acme-corp/ios-net-eng"
    paths:
      - "net"  # This is interpreted as $REPO_ROOT/src/ios/net

# src/aviator-config.yaml
owners:
  - team: "acme-corp/engineering"
```

You can place this config at any directory. If you place the files as indicated in the example, it will have the same effect as `.aviator/OWNERS` file in the example above.

Same as .aviator/OWNERS file, a file's owner is inferred based on the most specific path pattern.

{% hint style="info" %}
### Which format should I use?

When you initially onboard to FlexReview, we recommend using `.aviator/OWNERS` file. This is because the file has a compatible format with GitHub `CODEOWNERS` and you can easily onboard without further modifications.

Later, you can gradually move the entries to distributed `aviator-config.yaml` files. This allows each team to manage their code ownership by themselves, and when they move the code, the ownership information can be moved along with it with the config file.
{% endhint %}

## Common owners for cross-team code review

When a PR modifies files owned by different direct owners, FlexReview tries to find a **common owner**. Let's say a PR modifies files under `src/ios/auth`and `src/ios/net`. The related owners are inferred as this:

* **Owners:** `@engineering`, `@ios-eng`, `@ios-auth-eng`, `@ios-net-eng`
* **Direct Owners:** `@ios-auth-eng`, `@ios-net-eng`
* **Indirect Owners:** `@engineering`, `@ios-eng`

If there are more than one direct owner for a PR, FlexReview tries to find the common owner based on the file paths. In this case, `@ios-eng` is the common owner of both teams, and it will assign a reviewer from that team.
