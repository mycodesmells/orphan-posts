# Creating a name for yourself with GitHub

Have you seen any job offers for developers recently? Have you ever been asked about your open source contributions during an interview? I bet you have! Chances are, that you had to drop your head and admit, that you have not left any traces of your programming skills outside your company's products. But as great as those may be, you might never be able to give that to your potential employer as you just don't own it. So how exactly can you put yourself on the map? Github is the place to start.

### GitHub popularity

Over time there have been multiple places that you could publish your code, but none have ever reached the popularity of Github. As for April 2016, there are 14M users and 35M repositories ([source](https://github.com/about/press)) which is mind-boggling. You probably have heard of places like BitBucket or Google Code (which was closed this year), but if you want to brag about your skills during your next interview, I strongly recommend Github.

It is very easy to get started there, as there are so many different repositories in various programming languages dealing with a vast range of problems. And it is very likely that you are using some 3rd party library, framework, package (or however your language describes dependencies) that is hosted on GitHub. And this is a perfect situation to get started working in an open source community.

### Workflow

You start by finding a repository that could really use your help. You may find a bug, see an interesting new feature that can be added, or see an issue (bug reporeted by somebody else) that you think you are able to tackle. To get started you need to **fork** that repository, so that a copy is created under your user directory (`some-account/repo-name` will become `your-name/repo-name`). Now you can start making changes.

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/github-forked-repo.png"/>

There are few things that you should have in mind when making changes to some shared repository. First of all, you should follow any conventions that are present in the code. Whether there are some indentation style, file naming or anything else. You are not in your local playground any more!

If you want to fix a bug and want to make a good impression, you should start by submitting an issue in the original repository **with some tests**.  That way if you show some failing tests you not only allow others to reproduce the issue, but at the same time you show that you know exactly what has to be done.

Once you are done with the changes, make sure that all tests are passing (not only yours). Look for some CI that is connected to the repository, such as Travis builds, so that you can run any scripts that could possibly mark your version as failing (besides tests it could be for example some lint check). If everything looks good to you, you should submit a pull request from your repository (from a branch you've made your changes to be exact), to the master branch of the original repo. GitHub will show your changes and ask for some description (don't skip this, as it really helps the reviewer). After submitting it's a waiting game.

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/github-show-diff.png"/>

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/github-submit-pr.png"/>

Code review is very important step in this workflow. You have to be patient - you want to see your changes in the repository as soon as possible, but the reviewers are people too. They have life (at least some of them do), so it might take a while until you get some feedback. The second thing is, that you must be ready and open for criticism. This is not because somebody doesn't like you (although it's possible, don't be cocky!), but as a reviewer you want to keep a certain high level of code quality or a specific style. You might have to make some adjustments to your code, but don't worry. It is also possible that your PR gets rejected. For example the feature you desire is not accepted, or your bug fix causes more pain than good. It's sad, but that's how it is sometimes. No hard feelings, you'll score next time.

### Your reward

There are multiple gains from your contributions in the open source community. First and foremost, you become better at what you do - you learn new techniques, styles, even languages sometimes. Secondly, your name appears on the contributors list for some external repository, which shows that you are skilled and passionate about what you do (you've spent some potential TV time working, which is always a good thing). Sometimes, if your changes are really big, you might even be mentioned in some changelog, which must feel really great. Last, but not least (not for me) is if you make some contributions regularly, your profile shows that you are consistently working in the community.

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/github-profile-graph.png"/>

The graph itself is not the most important thing, but it's awesome  to see that you are improving code not only for your company's clients, but also for people all over the world.