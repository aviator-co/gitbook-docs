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
These optional owner configuration files enhance FlexReview's capabilities by providing more flexibility than the traditional CODEOWNERS file.
{% endhint %}

To address the limitations of the CODEOWNERS file, we introduce two optional files:

* `.aviator/OWNERS`
* `aviator-config.yaml`

#### Aviator OWNERS file

The `.aviator/OWNERS` file follows a similar format to GitHub's `CODEOWNERS` but allows only one team per line. However, ownership interpretation differs:

* **Owners**: All matching teams are considered owners of a file.
* **Direct Owners**: The team with the longest matching path.
* **Indirect Owners**: All other matching teams except the direct owner.

**Example:**

```python
# .aviator/OWNERS
src             @acme-corp/engineering
src/ios         @acme-corp/ios-eng
src/ios/auth    @acme-corp/ios-auth-eng
src/ios/net     @acme-corp/ios-net-eng
```

For a file under `src/ios/auth`, the ownership is:

* **Owners:** `@engineering`, `@ios-eng`, `@ios-auth-eng`
* **Direct Owners:** `@ios-auth-eng`
* **Indirect Owners:** `@engineering`, `@ios-eng`

FlexReview recognizes ownership recursively based on directory structure.

#### aviator-config.yaml

The `aviator-config.yaml` file provides an alternative way to configure ownership with a structured format and can be placed in any directory within the repository.

**Example:**

```yaml
# src/ios/aviator-config.yaml
owners:
  - team: "acme-corp/ios-eng"
  - team: "acme-corp/ios-auth-eng"
    paths:
      - "auth"
  - team: "acme-corp/ios-net-eng"
    paths:
      - "net"

# src/aviator-config.yaml
owners:
  - team: "acme-corp/engineering"
```

* The `paths` field is optional; if omitted, all files in the directory (recursively) belong to the specified team.
* Paths are interpreted relative to the directory containing `aviator-config.yaml`.
* Ownership follows the most specific path match, similar to `.aviator/OWNERS`.

{% hint style="info" %}
## Choosing a Format

For initial onboarding, we recommend starting with `.aviator/OWNERS` since it closely resembles GitHub's CODEOWNERS file, making adoption easier.

Over time, you can transition to `aviator-config.yaml` files. This approach allows teams to manage their ownership independently and ensures ownership information moves along with the code when it is relocated.

By leveraging these files, FlexReview provides a scalable and flexible way to define ownership in your repository.
{% endhint %}

## Common owners for cross-team code review

When a PR modifies files owned by different direct owners, FlexReview tries to find a **common owner**. Let's say a PR modifies files under `src/ios/auth`and `src/ios/net`. The related owners are inferred as this:

* **Owners:** `@engineering`, `@ios-eng`, `@ios-auth-eng`, `@ios-net-eng`
* **Direct Owners:** `@ios-auth-eng`, `@ios-net-eng`
* **Indirect Owners:** `@engineering`, `@ios-eng`

If there are more than one direct owner for a PR, FlexReview tries to find the common owner based on the file paths. In this case, `@ios-eng` is the common owner of both teams, and it will assign a reviewer from that team.
