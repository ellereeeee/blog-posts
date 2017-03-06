Yesterday I finished Udacity's [How to Use Git and GitHub](https://classroom.udacity.com/courses/ud775/lessons/3105028581/concepts/30953886320923#) course.

The end of the course taught me what to do when you make a pull request, but your code conflicts with some other code that was merged into the master repo after you cloned the master repo and before you made the pull request.

In this situation, you need to update your local origin/master branch with the remote master branch (not your forked master).

The workflow would look something like this:

1) `git remote add upstream [remote url]` Here we add a remote that points to the master branch. In these situations, it's common practice to name the branch "_upstream_." Remember that the "origin" remote points to our forked repository.

2) `git checkout master` then `git pull upstream master`. By checking out master and then running pull, we merge the updated master branch from upstream into origin/master.

3) `git checkout [feature branch]` then `git merge master [feature branch]`. This brings the feature (or temporary branch, whatever you want to call it) up to do with the master branch, which was updated in step 2.

4) `git push origin [feature branch]`. This updates the feature branch that you made with the pull request. Now your branch should have no conflicts and be ready for a merge!

This was a really great course and I recommmend it to anyone completely new to Git and GitHub. They explained the concepts behind Git and GitHub, showed how they were interconnected, gave you exercises to practice using the command line interface, and made you reflect what you learned in text files. 

As a coding newbie, I heard about Git and GitHub all the time and it feels awesome to finally be able to use this in my workflow. Finally I can code locally :D!

I've been practicing using Git and GitHub by pushing these posts to GitHub as Markdown files, which you can find in my [blog-posts repository](https://github.com/ellereeeee/blog-posts).

I look forward to using Git to re-create my [shoddy portfolio](http://s.codepen.io/ellereeeee/debug/YqdrNg/yPMJjGjNdZOM) once I finish reading _YDKJS - this and Object prototypes_.