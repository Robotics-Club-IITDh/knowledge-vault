Git is structured that it has 3 main stages you should care about :
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

###
So our workflow is basically
Make changes in our working directory -> 
Add the parts we want to commit to the staging area -> 
Check that the project in the staging area works as intended if necessary -> 
Commit the new snapshot to our repository

