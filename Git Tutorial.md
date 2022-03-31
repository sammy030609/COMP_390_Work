Git Tutorial for Adding Small Projects to GitHub from Command Line and Useful Tips using Git
=========
The purpose of this tutorial is to teach a user how to upload their small coding project to GitHub using command line. This tutorial will also cover the use of stashes, branches and merging in Git.

Step 1 - Making a New Repository
-------
Open up your internet browser and go to the [GitHub](https://github.com/) website. Create an account if you don't have one already. Once you have an account, click the **(+)** button next to your profile pic in the right corner and click **New Repository**.  After doing so, fill in the new Repository name and description of your new project. ![New Repository Screen](https://git-scm.com/book/en/v2/images/newrepoform.png)

Step 2 - Open Command Line App
-----------
After you have created a new repository, open up Terminal or cmd on your computer. You want to navigate to the directory in your computer that has your project in it. The command to navigate to the directory is:


Windows: 
`cd/directory_name_where_project_is`

Mac:
`cd directory_name_where_project_is`

Step 3 - Initialize Directory and Add to your Local repository
------
Now that your are in your project's directory in Terminal or cmd, you can initialize the directory by typing the command: 

`git init`

After the command has finished, you can add all the files from that directory into the local repository by typing: 

`git add .`

The add command will move the files to the staging area for the next step. and allow the files in the directory will be ready for committing to the new repository.

Step 4 - Commit to  the Repository and Adding a Remote Repository
------
When committing something in Git, it adds to file to the Local Repository on your computer which allows those files to be uploaded to GitHub for the final step. Type the command to commit the files: 

`git commit -m "A commit note"`

I would name the commit after what you are uploading to GitHub like "Coding Project".

The next thing that will need to happen is adding a Remote Repository url so that you can add any files to it that you want to push to your GitHub repository. 

Go to your new repository that you created in GitHub and press the green **Clone or download** button in the right side corner. Below it will pop up a link with your username in it and the repository name you created, like (https:github.com/user_name/new_repository_name.git), and copy the link.

Go into terminal and type:
```
git remote add origin https:github.com/user_name/new_repository_name.git
```
After you press enter, you can now push your project to your local repository.

Step 4 - Pushing from the Local Repository to GitHub
---------
To upload your project on GitHub after doing all the previous steps, type this into Terminal:

`git push origin master`

Congrats, you know have a new project in your GitHub repository!

Stashes in Git
-----
Stashing in Git is useful when you have made changes in a file, but end up changing to a different file midway through editing and aren't ready to commit those changes. 

To **STASH** a file, use the command  `git stash`
This will save the changes from the file you're working on and put it in a stack. You can also stash a file with a message by typing: 
`git stash save "Your message".`

To see the **LIST** of stashes you have made use the command: `git stash list`
The most recent stash is listed at the top and you can view the message you created (if applicable).
![Stash List](https://cdn-media-1.freecodecamp.org/images/757jZHhanVirv5F5ZBeTXi2XNVPyAhOmDgwV)

To **ADD** the commits you have made back to the file, you can either do `git stash apply` which takes the stash from the top of the stack or most recent one and applies the edits to that file or specifically pick a stash to apply to a file use
`git stash apply stash@{2}`. Doing the ADD commands won't delete the stashes and will keep them on the stack.

To **ADD** the commits you have made to the file and delete the stash you created, you can use the command `git stash pop` to reapply the most recent created stash and it will delete it from the stack. If you want to pop a specific stash, use `git stash pop stash@{2}`.

To **SHOW** the stash summary, use `git stash show`. 
![Stash Show](https://cdn-media-1.freecodecamp.org/images/W6tFM8O0xrUfFznYg9O-mvAND4zNDX2R-RFc)
If you want to see the full diff of a stash use the command:
`git stash show -p`

To create a **BRANCH** for your stash, use the command:
`git stash branch <name> stash@{#_stash}`. 
This helps when there are conflicts when adding or popping your stash. 

To **CLEAR** (delete) all the stashes made, use the command `git stash clear`

To **DROP** a specific stash created, use the command `git stash drop stash@{#_stash}`


Branching in Git
-----
When branching in Git, it allows the user to create a copy of a commit and modify it without editing/changing the original commit. It can help when editing someone else's code or fixing a paragraph. Every time the user edits the branch, it saves new commits in the branch that are meant to be deleted after the work is completed and finalized. 

To **CREATE** a Branch, use the command 
`git branch <name_of_branch>`
Doing this command only will create the new branch so if you want to add commits to it, you need to use checkout to switch to the branch.

To **CHECKOUT** the branch use the command `git checkout -b <branch_name>`. Now you can use git add and git commit commands to the new branch.

To **LIST** all the branches in the repository, use the command `git branch` or `git branch --list`

To **DELETE** a branch after you have finished working on it, use the command 
`git branch -d <name_of_branch>`
This command won't delete the branch if it hasn't been merged yet, which I will explain in the next section.

To **FORCE DELETE** a branch with unmerged changes, use the command 
`git branch -D <name_of_branch>`


Merging in Git
----
Merging in Git is when you have made all the changes you wanted to in a Branch and are applying them to the commit that was branched off of. 

To **MERGE** onto a branch in a local repository to the Main branch, use these commands:
`git checkout main`
`git merge <branch_name>`
The merge command allows you to take the changes/commits you made and integrate them into a single branch which will be the main branch. 
![Merge Visualisation](https://miro.medium.com/max/800/1*iB8lNrITmLvKeL8mnp3qAA.png)

To Merge a branch to a Remote Repository, use the command: 
`git push --set-upstream origin <branch_name>`
This will set the upstream branch to be pushed to the remote repository so it can be uploaded to GitHub.
![Merge Remote Repository](https://devconnected.com/wp-content/uploads/2019/10/upstream-1-1024x702.png)

To update the main branch you have been working on if the original author makes changes, use the commands:

`git checkout <branch_name>`
`git merge main`

Doing these commands will update your branch with the new work that was added to it from other people. It will keep your branch up to date with the new content.
![Update Main Branch](https://info.varonis.com/hubfs/Imported_Blog_Media/git-merge-to-branch-2.png)




