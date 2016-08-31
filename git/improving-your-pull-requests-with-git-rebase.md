# Improving Your Pull Requests with git rebase

Have you ever looked at your project's git history and wondered why are there so many commits describing the same things net to each other? Or why there are so many commits that fix tests not updated after a previous one? On maybe you have make a silly mistake and feel embarrassed that you've already committed you changes and have to admit to the whole world that you made it? Rebasing your history might be just what you need...

### Importance of PRs

Working in a group might be difficult, especially when people have different approaches to solving same issues. This is why one of the most important thing in developing together is achieving some kind of agreement on code style, formatting, etc. As it is hard, not to say impossible, to gather a couple of people writing code in the same style, this consensus is achieved via code review - once a developer finishes their task, they submit a _pull request_ (sometimes called _merge request_), which is assessed by others. Then, if it's good enough, it can be merged into master branch.

Code review is not easy, so if we want to get as much useful feedback as possible, we should make it as simple as possible to the reviewer. One of the things that can irritate more than anything else is that your PR is just too big. It might be logical for you to apply many changes at once, but for someone to try to understand too much at the same time can be very hard.

### Keeping your history clean

Another thing that you should keep in mind is your git history. As your project grows, the log obviously consists of more and more entries, so it might be difficult to find anything there if you don't keep it clean. One of the best ways to do that is make your PRs as small as possible, that is limit them to one commit, maybe two tops. But do you have to just make all the changes, commit just once and push it to the review? What about the concept of committing as often as you make some significant progress? You should definitely stick with the latter, so how do you achieve both at the same time? Enter `git rebase -i`.

### Real-life example

Let's say that you have a task that requires you to make changes to your database model, user interface and tests. You start with one and commit with a message:

    Update DB model after requirements change

Then you go to user interface, and add another one:

    Make necessary UI changes to reflect new model structure

Finally, you go to the tests (you somehow haven't done them alongside code changes, how come?) and commit yet again:

    Make tests pass

As soon as you are happy with your solution, you realize that you need to make one more tiny change:

    Change "status" field to enum

Now you are ready to publish your changes. Your history looks like this:

    e02b5da - (my-branch) Change status field to enum
    c817b1c - Make tests pass
    48bba4b - Make necessary UI changes to reflect new model structure
    71462b2 - Update DB model after requirements change
    b07c87a - (origin/master, master) Initial commit

Notice that if you push it like this, you'll have four commits in your `master` branch history describing one change. It's not the way it should be, I think we can all agree. This is where `git rebase -i` should be used.

This command takes one parameter, which can be a commit (hash or one's position relative to current HEAD), branch name or tag. I personally use it to clear everything up to `origin/master` because this is the branch I'll want my changes to be merged into:

    git rebase -i origin/master

Now git will open my history in my preferred text editor, in my case its vim, and show me how it looks like, as well as some actions I can perform:

    pick 71462b2 Update DB model after requirements change
    pick 48bba4b Make necessary UI changes to reflect new model structure
    pick c817b1c Make tests pass
    pick e02b5da Change status field to enum

    # Rebase b07c87a..e02b5da onto b07c87a (4 command(s))
    ...

Notice that every commit is preceded by `pick` word. It means that if every commit will stay exactly the same. However, there are a couple things I can do with them. In this case, we want to _squash_ commits, which means that changes from one commit will be included into a previous one. Then, we can alter their message, so that the one (or more) commits that remain accurately reflect your changes. So in our case, we replace `pick` with `squash` for every commit except the first one (as at least one has to stay to gather all squashed changes):

    pick 71462b2 Update DB model after requirements change
    squash 48bba4b Make necessary UI changes to reflect new model structure
    squash c817b1c Make tests pass
    squash e02b5da Change status field to enum
    ...

We can save and exit the file, and see the next step, which is rewriting commit message (again opened in my text editor):

    # This is a combination of 4 commits.
    # The first commit's message is:
    Update DB model after requirements change

    # This is the 2nd commit message:

    Make necessary UI changes to reflect new model structure

    # This is the 3rd commit message:

    Make tests pass

    # This is the 4th commit message:

    Change status field to enum

    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.

Here, everything that remains that is not a comment, will be included as our lone commit message. So we can delete everything and write:

    Update DB and UI after requirements change

After saving and leaving this file, we get in our console something similar to this:

    $ git rebase -i origin/master
    [detached HEAD 8aaf8ee] Update DB and UI after requirements change
     Date: Wed Aug 31 18:49:19 2016 +0200
     1 file changed, 1 insertion(+), 1 deletion(-)
    Successfully rebased and updated refs/heads/master.

Now our git history is much clearer:

    8aaf8ee - (my-branch) Update DB and UI after requirements change
    b07c87a - (origin/master, master) Initial commit

Which makes our PR, as well as our git history, much better to look at.

### Git rebase use cases

Apart from squashing commits before PRs, it can be used (at least I do it) for things like:

- hiding our mistakes (get rid of _fix failed tests from previous commit_ messages),
- typos in commits (especially if it's not the latest commit),
- adding changes suggested by code review without adding silly _Changes after code review_ commit,
- keeping all related changes next to each other (`git rebase` apply commits one by one, so that if your PR make two separate changes, they will appear in git history next to each other)
- pro tip: deleting commits - this is done by deleting a line in an original list of edited commits (the one with `pick` and `squash`, etc). For example if you want to hardcode some values but you never ever want to push it to `master` branch, you can do it here.

### What about conflicts?

If you rebase with a branch that has been modified in the meantime, you can face merge conflicts. What happens there? `git rebase` will act as if you'd apply each commit one by one, to the latest state (last commit) of that branch. If a merge conflict occurs, `git` will stop and notify you about that. Then after you make appropriate changes and resolve them, you need to enter following commands:

    git add -u
    // NEVER commit!!
    git rebase --continue

Repeat it until you reach your last commit to see your history rewritten. If, at any point you decide that you want to revert and not complete you rebase, you can abort in with:

    git rebase --abort

### Summary

In my opinion, `git rebase -i` is a very powerful command that should be used by every developer before every PR. I use it all the time, just to make sure that my git history looks as clear and as smooth as possible. If you'd like your to be the same and never see _Fix previous commit_  ever again, this is something for you.
