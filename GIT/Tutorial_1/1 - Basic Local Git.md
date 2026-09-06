## **What is GIT?**
When I first started coding and making larger projects on my computer, I used to save everything in a folder.
Every new version/major improvement I made, I saved in a new folder labelled project_v2 or v3 something of the sort.
And so it kept growing - project_v7, project_final, project_real_final, project_FINAL_final_final
All saved in a new folder.
If I was working with someone else, they would send me the code on whatsapp and I would copy paste it, then do the same thing and create a new folder.
That was my approach which in its own right worked, but it had a lot of inconveniences.
If I wanted to refer to previous versions I had to open those seperately and look at it.
I had to look hard to find the changes i had made.
If my friend made changes themselves and sent it to me, I had to go through all of it and compare which parts were different from the changes I had made.
The naming convention for folders having my projects also broke down, and so on.

So if we were to make some software to solve these problems, this is what we would want from it :
- Allow us to work in a single directory
- Create 'snapshots' of the directory and save them when we want it to
- Every snapshot to be linked in a graph, showing all the previous versions and how they are related
- To be able to create branches from a snapshot 
- To be able to then merge these branches with minimal effort
- To be able to navigate between all these snapshots of different versions and branches
- Some other minor things like adding comments with each snapshot so we know what changed, etc

Git was made as this solution and implements this very well .
Let's take a look at how git implements this and how to use git for this .

## **Git structure and workflow**
Git is structured such that it has 3 main stages you should care about :
### *The Working Directory*
Your working directory is the directory in which you have all the files of your projects. 
In real time, you make changes to the files in your working directory.
Basically its the folder on your laptop where you work.

### *The Repository*
The repository, or repo for short contains your final code snapshots and their complete history.
It contains all the information on the graph we talked about before and all of the contents of the project at each snapshot in the graph .

### *The Staging Area*
The staging area acts as a place to organise and plan our next commit.
It is the bridge between your working directory and repository,
While not necessarily needed, it helps us a lot in keeping things organised and handling our project easily
So our workflow is basically
Make changes in our working directory -> 
Add the parts we want to commit to the staging area -> 
Check that the project in the staging area works as intended if necessary -> 
Commit the new snapshot to our repository

Let's start learning how it works by making a project and using it.
I'll be using bash to create folders and files, feel free to use your file manager/explorer instead. I'll be assuming that you're using bash for the git commands anyways so it might be easy enough to follow my commands to do everything in bash rather than switch between the two.

## **Initialising a repo**
What is a repository?
A repository is the graph/map of your complete history.
Every time you take a snapshot of your project, it is added to the repository.
For our project, i'm going to write a blog in a txt file.

```bash
cd ~/Documents
mkdir myBlog
cd myBlog
echo "Hi my name is tushar" >> blog1.txt
```

Now that my blog is made let's check what's in the folder.
```bash
ls -a
```
```bash
.  ..  blog1.txt
```

You should see nothing in the folder except our text.
Now lets initialise git and see what changes.

```bash
git init
ls -a
```
```bash
.  ..  blog1.txt  .git
```

You can see that all that initialising git did was make a new folder, .git.
Lets take a look inside .git.

```bash
ls -a .git
```
```bash
.  ..  
branches  
config  
description  
HEAD  
hooks  
info  
objects  
refs
```

Okay so a lot of stuff inside this git folder.
We don't need to know this to use it, so I'll just give a brief overview of what happens here.

HEAD : A pointer pointing to the current snapshot you are working on.
objects/ : A folder containing all the data of all your snapshots, the graph, etc. It has all been compressed and saved here.
refs/ : A folder which stores pointers to all your branches's latest snapshots.
config : Some settings for this repo.

From now on, I will be using 'commit' and 'snapshot' interchangably.
The rest are not that important.
So when you change the commit you are working on, the HEAD pointer changes.
When you commit new data, new objects are saved in the objects/ folder.
When you make a new commit to a certain branch, the pointer to that branch changes in refs/ .

You don't need to know any more theory or internal working. You didn't even need to know this much to just use git, but it's good to have an understanding of it.
If anyone is interested in learning more about how it works, I would suggest learning when you have free time - it's kind of interesting.

Let's get back to using git.

## **Staging, Commiting, and Status**
Let us check the status of our repo.
When I say repo I'm referring to the graph of commits and all the data of all commits.

```bash
git status
```
```bash
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	blog1.txt

nothing added to commit but untracked files present (use "git add" to track)
```

So it says we haven't made any commits yet, and that we have not tracked our blog. Both of which are true.

Before we make a commit, git needs us to track and stage a file. 
Basically we have to say to git that 'We want to include this file in the next commit', and add it to the staging area.
We can do this using the 'add' command.

```bash
git add blog1.txt
```
or
```bash
git add .
```

'add .' adds all the files in your current directory to the staging area.
The staging area is basically a blueprint of what you want your next commit to look like.
Adding the same file twice to the staging area wont duplicate it there. 
Adding a file will only update its copy in the staging area, changing it from the copy we had when we last added it.
If you stage a file, then delete it, its non deleted version is still being tracked. So you have to stage the delete by using add
```bash
rm filename.ext
git add filename.ext
```
or you could use gits command to do both together
```bash
git rm filename.ext
```
if you want to remove a file from the staging area but want to keep it in your local directory, you can use
```bash
git rm --cached filename.ext
```
~~mv command also~~

We still haven't made a commit, so let's make a commit and check our status.
```bash
git commit -m "My first blog"
```
```bash
[master (root-commit) bb40d65] My first blog
 1 file changed, 1 insertion(+)
 create mode 100644 blog1.txt
```
```bash
git status
```
```bash
On branch master
nothing to commit, working tree clean
```

That's our first succesful commit, and the first node on our graph.
We can look at the graph using

```bash
git log --graph --oneline --all --decorate
```
```bash
* bb40d65 (HEAD -> master) My first blog
```
Only one node because we only made one commit now.
The code you see there - "bb40d65" in this case is the Hash of the commit.
Basically, it is a unique identifier for this commit. You could say that that code "bb40d65" is the ID number of this commit.

I want you guys to make 2 more commits now, testing this out -
1) Make some changes to your blog, add it to the staging area, change the blog again but dont stage it, and make a commit with  a message about what you did.
2) Make some change to the blog again - create a new txt file blog2, then stage the whole directory(blog 1 and 2) and commit it with a message about what you did.

```bash
echo "Today I learned about Git staging and commits." >> blog1.txt
git add blog1.txt
echo "This line is NOT staged yet!" >> blog1.txt
git commit -m "Add second entry to blog1"
```
```bash
echo "My second blog post about terminal commands." >> blog2.txt
git add .
git commit -m "Add blog2 and finalize edits"
```
This is what I ran.
Checking our graph,
```bash
git log --graph --oneline --all --decorate
```
```bash
* 1fa68c4 (HEAD -> master) Add blog 2
* e6f7f4f Add second entry to blog1
* bb40d65 My first blog
```

Okay we have something : a linear timeline made of 3 distinct nodes in our graph.

## **Linear Navigation**
Now lets learn how to navigate between the 3 commits we made.
Use the command checkout to go to your first, original commit.

```bash
git checkout bb40d65
```

Look at your folder.
Blog 2 is gone, and blog 1 has been restored to your first commit.
Everything you did after is still saved, but now we can work on this and switch between commits very easily.

Use the same command with the correct hash to  move to your second commit.
Take a look at everything there.
The first change you made to blog1 is there. 
However, notice that the change you made after staging blog 1 and before commiting is not present.
This is because only the data in your staging area gets commited, and your staging area only updates if you run the command to update it.

To go back to our latest commit, we can use 
```bash
git checkout master
```

'master' is the main branch we will be working in.
It is also called 'main' sometimes.
Using the checkout command with a branch name will take you to the latest commit in that branch.

Notice now that you have both your blogs and blog1 has all the changes you made. 
Why did the final change to blog1 get counter?
We only created blog2 after our last commit right? 
Find out.

The answer is that we staged the whole directory, including the changes to blob1 we made before the last commit that wasn't staged.
Note that our working directory can change however we want and we can keep staging what we want selectively and making commits.
As long as you dont use commands like checkout (or pull which you will see later) which change your working directory, there wont be any information lost, and you can add and commit uncommited changes later.
However, if you don't stage a change, and change the contents  of your working directory using checkout, there may not be a way to get the unstaged changes back

I want you guys to try this - 
Make a change, do not stage it, and go to a previous commit. 
It may give you a warning that youe changes will be lost, which should be indication enough. 
Let's do it anyways using -f .
Then come back to your latest commit and see if its still there
```bash
echo "Change no 3" >> blog2.txt
git checkout -f bb40d65
```


## **Stash**
Now in the warning you saw, what did it tell you to do?
It said either commit your changes or stash them.
Let us look at stash.
Stash is a temporary storage for your working directory.
If you have any unsaved changes you dont want to lose.

Lets try. Make some changes, and dont stage them.
```bash
git stash
```
```bash
git stash list
```
Checkout to an old commit.
Don't change anything.
then come back to the latest commit and check if your changes are there.
They won't be there.
Use stash pop, and your changes are back.
```bash
git stash pop
```
## **Restores and Undos**
Now there might be times where you've recently made some changes you want to undo.
Let's make a new commit.
Add some text to your blog 1, save and stage it.
Now add some nonsense to blog1.
If you want to restore your blog1 to what you had staged, you can run 

```bash
git restore blog1.txt
```

This will restore blog1 to what you had staged.
If you want to get rid of what you had staged as well in blog1, you can run

```bash
git restore --staged blog1.txt
```

Check your text file and see what changed.

That works for when theres a mistake in your workind directory or staging area,but for when you make a mistake in your commit and want to undo it, or want to update 1 or 2 lines without having to make a whole new commit there are a few other commands.

```bash
git commit --amend
```
This works by commiting any files you forgot to add or update previously. It makes the changes in the most recent commit itself and doesn't create a new one.

Let's try it on our blog.
Add a new line in blog1 and stage it.
Then use the amend command.
```bash
echo "One last line I forgot to include" >> blog1.txt
git add blog1.txt
git commit --amend --no-edit
```
The `--no-edit` flag tells Git to keep your existing commit message while updating its contents.
You can also use it to change the commit message using `-m` instead.

If you made a commit you want to undo, you can use `git reset`
The reset command Rewinds the last commit, while keeping or wiping your working directory and staging area depending on the argument you give.
All of them rewind your commit to the commit which's hash you provide.

```bash
git reset --soft <hash>
```
Keeps working directory and staging area preserved

```bash
git reset --mixed <hash>
```
Wipes staging area

```bash
git reset --hard <hash>
```
Wipes both working directory and staging area

You can replace the hash with HEAD~N to go N commits backwards.
reset defaults to mixed reset.

Let us go one commit backward
```bash
git reset HEAD~1
```

Now check your graph and files.
What happened?
Your files are saved but your latest commit is erased.
So our working directory has unsaved changed.
Let's amend the latest commit to include our changes and have another message.

```bash
git add .
git commit --amend -m "Amending"
```


## **Ignoring Files**
Sometimes you have files in your folder which you dont want to commit.
Could be local environment variables, crash reports, logs, etc.
All the things we don't want to track, we add in the .gitignore file
Let's make some files we don't want to commit.

```bash
echo "Secret_Key=SuperSecret" >> .env
echo "Error at 10:00 AM" >> server.log
```
```bash
git status
```
You see that it shows as untracked files.
Let's add them to the .gitignore file.
```bash
echo ".env" >> .gitignore
echo "server.log" >> .gitignore
```
```bash
git status
```
We include the .gitignore file in our next commit

```bash
git add .
git commit -m "Added gitignore"
```

Note : Gitignore only ignores untracked files. If any of your files were already added before adding them to gitignore, git will continue to track them.
You can stop them from being tracked after they were added by using 
```bash
git rm --cached .env
```
## **Revision** and Exercise
Lets revise what we did so fat.
Git has 3 stages - The working directory, Staging area and Repository.
We learnt how to initiralise, stage, commit, check graph and status, navigate linear commits, stash and restore.
Take 5 minutes and do this to revise it.
Make a new folder outside this directory.
1) Create a text file and initialise a git repo in your directory.
2) Write something, stage and commit it.
3) Add a new file and add its name to your gitignore file.
4) Write something else, stage and commit again.
5) Write something else, do not stage, stash it, check stash list to see whats in stash.
6) Use the log command to see your commit graph and note down the hash of your first commit.
7) Navigate to your first commit using checkout , then navigate back to the latest commit.
8) Pop stash and stage the changes without commiting.
9) Write something in the text file and do not stage it.
10) Restore the text file to whats in the staging area.
11) Restore the staging area to discard the uncommited changes to that file.
12) Amend your latest commit with a new, different message.
13) Mixed reset to your last commit.
14) Check what changes have happened and trace in your mind what every command has done.
