Okay say that the blogs that you write in the master branch are being hosted on some website, and you want to write a new big blog.
The new blog will take about 3 days to write, and you want to use all the features of git while writing it.
However, you don't want to commit the incomplete blog to your master branch and have it shown on the website.
To tackle this, you can create a branch.
A branch allows you to create another isolated timeline of snapshots of sorts.
You can make messy, experimental commits without affecting the stable line of code. Once your feature is tested and working, you merge it back.

%%Example of friend working on a blog at the same time%%

Let's get straight into it.

## **Creating and using a branch**
Let us list out all the branches using
```bash
git branch
```
```bash
* master
```
The * and green text show which branch the head pointer is currently pointing to.

Lets create a branch
```bash
git branch bigblog
```
```bash
git branch
```
```bash
  bigblog
* master
```
We made a new branch, and we are currently still on the master branch.
To switch branches, use 
```bash
git checkout bigblog
```
or
```bash
git switch bigblog
```

You can also create and switch together using either of these
```bash
git checkout -b bigblog
```
```bash
git switch -c bigblog
```

Let us make a new blog in our branch and commit
```bash
echo "This is the start of my biggest blog ever" >> blog3.txt
git add .
git commit -m "3rd blog"
```
Lets check our graph
```bash
* ae14085 (HEAD -> bigblog) 3rd blog
* 2c481d2 (master) Added gitignore
* f5d8cbf Amending
* 4c36505 My first blog
```
See that our master is still behind.
Our head now points to our bigblog branch.
Check all the files present in your folder.
Switch back to master and check again.
It seems to be working well.

## **Merging**
There are 2 scenarios when you try to merge a branch :
#### Fast Forward Merge
When the branches you want to merge are sequential. ie if one of the branches is an ancestor of the other one.
In this case, master is the parent of bigblog.
Fast forward merge brings the pointer of the ancestor to the child branch.
```
Before merge :
Commit1 -> Commit2 (master) -> Commit3 (bigblog/HEAD)
After merge :
Commit1 -> Commit2 -> Commit3 (master,bigblog/HEAD)
```
Let us do this by running
```bash
git switch master
git merge bigblog
```
```bash
* ae14085 (HEAD -> master, bigblog) 3rd blog
* 2c481d2 Added gitignore
* f5d8cbf Amending
* 4c36505 My first blog
```

####
Okay that seems to have worked.
Now let's try making 2 parallel branches.
We can continue with our bigblog branch, and also add a bigblog2 branch
```bash
git branch bigblog2
```
Let's add a line to our blog3.txt in both bigblog and bigblog2 branches

```bash
git switch bigblog
echo "New entry1" >> blog3.txt
git add .
git commit -m "New entry"
```
```bash
git switch bigblog2
echo "New entry2" >> blog3.txt
git add .
git commit -m "New parallel entry"
```
Lets check our graph
```bash
git log --graph --oneline --all
```
We can see that bigblog and bigblog2 diverge from our master.
Let us now try merging them in something called a 

#### 3 Way Merge and Merge Conflicts
Let us try this
```bash
git switch bigblog2
git merge bigblog
```
```
Auto-merging blog3.txt
CONFLICT (content): Merge conflict in blog3.txt
Automatic merge failed; fix conflicts and then commit the result.
```

You see that it says there's a conflict.
And that makes sense, right?
We added a new line to blog 3 in both the branches, so when we try to merge them, git can't know what to do.
This is what we call a merge conflict - a situation where branches you want to merge have differences in the same place and you need to decide what to do.
Either bigblog2 overwrites bigblog, vice versa or you keep both of their contents to some extent.
This is something you have to manually do.
Run `git status` to check your status
It says you have an unmerged path blog3.txt
Let's take a look into blog3 to see what changed.
We see this
```
This is the start of my biggest blog ever
<<<<<<< HEAD
New entry2
=======
New entry1
>>>>>>> bigblog
```
This format (<<<,\=\==,>>>) is git telling us there is a merge conflict.
What git has done here is that it's showing us the differences between blog3 in both the branches.
It wants us to edit the file andr emove the marker lines (`<<<<<<<`, `=======`, `>>>>>>>`) and combine or choose the final text you want to keep.
If you didn't expect this and want to cancel the merge, you can run 
```bash
git merge --abort
```
Now on inspecting blog3 we can see that its back to normal in our branch.
We want to merge them though, so let's merge them again and resolve the merge conflict.
```bash
git switch bigblog2
git merge bigblog
```
I run into the same merge conflict again, but this time I'm editing my blog3.txt to look like this
```
This is the start of my biggest blog ever
New entry2
New entry1
```
Now I have to stage and commit this change
```bash
git add .
git commit
```

#### Cleaning up and deleting unnecessary branches
Now if you check the graph, you can see that we have merged the two.
The pointer for master is still behind though, so let's fix that.
```bash
git switch master
git merge bigblog2
```
Since we are done with the bigblog branch, we can delete the pointer to it.
We have master so we don't need bigblog2 either.
```bash
git branch -d bigblog bigblog2
```
Now if you look at the graph you'll see a clean history with just the master branch left


## **Rebasing**
As you can see, merging works decently.
However, if your project gets huge and there are a lot of merges, the repos history becomes messy very fast.
It's harder to find exactly where a certain change happened because you have to search through the whole graph.
Sometimes after you finish working on your branch, you want to put it on top of your master like you were working on master the whole time.
This is where we can use something called rebasing.
If we initially have a graph like this
```
        C --- D [bigblog] 
       /        
A --- B --------- E [master] (Merge Commit)
```
While merge would handle this as so
```
        C --- D [bigblog] 
       /        \
A --- B --------- E [master] (Merge Commit)
```
Rebase acts like this
```
A --- B --- E --- C'--- D' [master,bigblog] 
```
What you want to use depends on the situation you are in.
Maybe if you finished working on the third blog, but you want to try adding a few lines to it without affecting the main branch, you can try it in a new branch, and if it works you can use rebase.
If you are making a new blog entirely and want to merge it later, maybe merge will give you a better history of all the changes.
Also note that if your branch has significant changes, you might have to resolve merge conflicts several times using rebase but only once using merge.
Let us try using rebase.
Let's  create a new branch and make some changes and commits.

```bash
git switch -c bigblogagain
echo "Another change" >> blog3.txt
git add .
git commit -m "Final change for blog3."
```
```bash
git switch master
echo "Blog 2 change" >> blog2.txt
git add .
git commit -m "Final change for blog2."
```
Check the graph
It has diverged again
Now if we run 
```bash
git rebase bigblogagain
```
We can see that they have been merged into a linear graph
We can once again delete the branch
```bash
git branch -d bigblogagain
```
If you run into a merge conflict while rebaseing, do not commit.
You need to clean up the conflict, stage it using git add, then run
```bash
git rebase --continue
```
I will not go through merge conflicts again because you fix them the same way we did while merging


## **Note**
There are still a bunch of things not covered - a couple of other ways to merge, undoing a rebase, checking differences between commits, etc. But this should cover the basics of local git


## **Revision and Exercise**
So all in all, we learnt how to create and use branches, how to merge, how to rebase, and how to resolve merge conflicts.
In the current directory, do these steps :
1) Make sure you are on the master branch
2) Create and switch to a new branch "b1b1"
3) Add a single line to blog1.txt, stage and commit.
4) Switch back to master
5) Create and switch to a new branch "b1b2"
6) Add a single line to blog1.txt, stage and commit.
7) Check your graph
8) Do a fast forward merge, bringing master to b1b2. 
   Check your graph again.
9) Once master and b1b2 are in the same place, delete b1b2 and use rebase on b1b1 to get a nice linear graph
10) Correct the merge conflict, then run `git rebase --continue`
11) Check your graph again
12) Delete the b1b1 and b1b2 branches