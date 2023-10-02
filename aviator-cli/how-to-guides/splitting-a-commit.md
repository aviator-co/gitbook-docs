# Split a Commit

Aviator CLI helps you splitting a large commit.

## Workflow

1. Check out the commit that you want to split
2. Run `av commit split`
3. Select diff hunks to add to the first commit. This is the same as `git add -p`
4. A commit editor pops open with the original commit message prefilled.
5. `git add -p` and `git commit` are repeatedly called until there's no diff hunks left.

## Example

In this example, we will split the following commit into two: First commit should have changes to `db.go`, and the second commit should have changes to `api.go`. Currently, the changes are made in one commit.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-13 at 1.23.28 PM.png" alt=""><figcaption></figcaption></figure>

We run `av split commit`. It prompts you whether you want to add the presented diff hunk to the first commit. In this example, we want to include only changes to `db.go`, so we respond with `n` for the changes to `api.go`, and `y` for the changes to `db.go`.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-13 at 1.26.07 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Under the hood, this runs `git add --patch`. Please see [Interactive Staging](https://git-scm.com/book/en/v2/Git-Tools-Interactive-Staging) for the detailed usage.
{% endhint %}

After choosing which parts should go to the first commit, it prompts you to enter a commit message. The commit message is pre-populated with the original commit's message that you want to split.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-13 at 1.26.50 PM.png" alt=""><figcaption></figcaption></figure>

Once you update the commit message, close the editor.

Since there are changes that aren't committed to the first commit, we will continue with choosing what to include in the second commit. It prompts you which diff hunks to be in the second commit.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-13 at 1.27.48 PM.png" alt=""><figcaption></figcaption></figure>

This time, we want to add all the changes to `api.go`, so respond with `y` for all.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-13 at 1.28.15 PM.png" alt=""><figcaption></figcaption></figure>

It pops open an editor for the commit message of the second commit. Update, save, and close the editor.

At this point, we split all diff hunks to commits. The `av commit split` command exits, and you can see that the original commit is split into two.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-13 at 1.28.35 PM.png" alt=""><figcaption></figcaption></figure>

## Cancelling the command

You might want to stop the splitting process sometimes. Even in that case, your branch is safe. The `av commit split` command will not touch the branch until the entire process finishes.

When a command is cancelled in the middle, it shows a command on how to get back to the original state when you start `av commit split`. Simply run `git switch --discard-changes $BRANCH_NAME` will get you back to where you were.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-13 at 3.00.57 PM.png" alt=""><figcaption></figcaption></figure>

