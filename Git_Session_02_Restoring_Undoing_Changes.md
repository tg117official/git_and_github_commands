# Git Session 2 — Undoing Changes and Recovering Files

**Tools:** Git Bash and Notepad  
**Practice files:** `customers.txt` and `orders.txt` in the Git repository from Session 1

> **Before starting:** Use your existing repository from Session 1. Both practice files should already have at least one commit. Begin with no unfinished edits: `git status` shows whether files have changes waiting to be saved in Git. If you are still viewing an older commit from Session 1, `git checkout main` returns to the current version on `main` (provided that was your original branch). Do not discard unfinished work just to make the repository clean.

## 1. Where is the current version?

- Run `git status` to see whether files have changed and whether those changes are ready for the next commit.
- Run `git log --oneline -3` to see the latest three saved versions (commits).

The **working tree** contains the files you see in Notepad. The **staging area** contains the file changes selected for the next commit. A **commit** saves that selected version in Git's history. **HEAD** identifies the commit currently being viewed, usually through the current branch.

## 2. Accidental edit: keep the last saved version

**Situation:** You edited `customers.txt`, but the new information is incorrect. You haven't staged or committed the edit.

1. Open `customers.txt` in Notepad, add `C999,Wrong Customer,Invalid`, and save the file.
2. Run `git diff -- customers.txt` to see what has changed in the file since its staged version.
3. Run `git restore -- customers.txt` to replace the working-tree file with the version in the staging area. Because this edit was not staged, the incorrect line is removed.
4. Run `git diff -- customers.txt` again to confirm there are no remaining unstaged changes.

**Remember:** `git restore -- customers.txt` discards unstaged edits in that file. Use it only when those edits are no longer needed.

## 3. Accidental staging: remove a file from the next commit

**Situation:** You changed `orders.txt` and selected it for the next commit, but you aren't ready to include those changes yet.

1. Open `orders.txt` in Notepad, add `O999,C999,9999`, and save the file.
2. Run `git add orders.txt` to place the current file changes in the staging area, ready for a future commit.
3. Run `git diff --staged -- orders.txt` to see the changes currently selected for that commit.
4. Run `git restore --staged -- orders.txt` to **unstage** the file: remove its changes from the next commit's selection **without deleting the edits from the file on disk**.
5. Run `git diff -- orders.txt` to confirm the edits are still in your working-tree file.
6. Run `git restore -- orders.txt` to discard those practice edits and return the file to its earlier content before the next scenario.

**Remember:** *Unstage* means **take changes out of the staging area**, not delete them from Notepad or from the computer.

## 4. Accidental deletion: bring a file back

**Situation:** You deleted `orders.txt` from your computer, but the deletion has not been committed.

1. Run `rm orders.txt` in Git Bash to delete the file from your working tree.
2. Run `git status` to confirm Git notices the deletion of a tracked file.
3. Run `git restore -- orders.txt` to recreate `orders.txt` using its staged version (normally the latest committed version).
4. Run `git status` again to check that the deletion has been resolved.

The file returns because its earlier contents were recorded by Git. This does not recover an untracked file that Git never saved.

## 5. Accidental commit: undo the change but keep its history

### A. Incorrect data was committed

**Situation:** An incorrect customer record was not only edited but also committed.

1. Open `customers.txt`, add `C999,Wrong Customer,Invalid`, and save it.
2. Run `git add customers.txt` to include the incorrect edit in the next commit.
3. Run `git commit -m "Add incorrect customer"` to save that mistaken version in history.
4. Run `git revert --no-edit HEAD` to create **another commit** that reverses the changes made by the latest commit. The incorrect line is removed from the current file.
5. Run `git log --oneline -3` to see both the incorrect commit and the new commit that undid it.
6. Run `git show HEAD~1` to inspect the incorrect commit even though its changes have been reversed. `HEAD~1` means the commit immediately before the current one.

### B. A deletion was committed

**Situation:** `orders.txt` was deleted and the deletion was committed. You now need the file back.

1. Run `rm orders.txt` to delete the tracked file.
2. Run `git add -u` to stage changes and deletions to files Git already tracks, including the deletion of `orders.txt`.
3. Run `git commit -m "Delete orders by mistake"` to save the accidental deletion in history.
4. Run `git revert --no-edit HEAD` to create a new commit reversing that deletion; `orders.txt` returns.
5. Run `git log --oneline -3` to confirm that both the deletion and its reversal remain in history.

**Remember:** `git revert` **does not erase** the bad commit. It creates a new commit that cancels the bad commit's effect. An accidentally committed password or secret remains in earlier history even after a revert, so it must also be revoked or rotated.

## 6. Recover one file from an older commit

**Situation:** You made a valid change to `customers.txt` and committed it, but later decide the file should look exactly as it did in the previous commit. Other project files should remain untouched.

1. Open `customers.txt`, add `C105,Kiran,Active`, and save it.
2. Run `git add customers.txt` to select the updated file for committing.
3. Run `git commit -m "Add customer Kiran"` to save the new file version.
4. Run `git show HEAD~1:customers.txt` to **view** how the file looked in the commit immediately before the latest one, without changing anything on disk.
5. Run `git restore --source=HEAD~1 -- customers.txt` to **copy that older file version into the working tree** without moving HEAD or changing other files.
6. Run `git diff -- customers.txt` to examine the difference between the restored file and the version currently staged.
7. Run `git add customers.txt` to select the restored file content for the next commit.
8. Run `git commit -m "Restore previous customer list"` to save the restored version as a new commit.
9. Run `git log --oneline -4` to verify that the commit adding Kiran still exists in history, even though the current file no longer contains that record.

`HEAD~1` works here because the desired version is exactly one commit older. For any other saved version, use `git log --oneline` to locate its commit ID, then substitute that ID for `HEAD~1` in `git show` or `git restore --source`.

---

### What to remember

- A file can be edited on disk without being staged or committed.
- Removing changes from staging keeps the edits in the working tree.
- Restoring the working tree can discard edits, so inspect the changes first.
- Reverting a commit keeps the original commit in history and adds a commit that reverses it.
- Viewing an older file with `git show` does not change the file; restoring it with `git restore --source` does.
- You can recover a single file without taking the entire project back to an older version.
