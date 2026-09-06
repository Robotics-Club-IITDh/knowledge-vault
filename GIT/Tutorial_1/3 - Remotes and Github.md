So far everything we've done has lived on our own laptop. Nobody else could see our blog, and if our laptop died, so would our repo.
That's the next problem to solve - working with other people, and keeping a copy of our repo somewhere other than our own machine.
That's what we'll use GitHub for.

## **What is GitHub?**
Git and GitHub are not the same thing, even though the names are almost identical and it trips people up all the time.
Git is the tool that does everything we did in part 1 - it runs on your machine and manages your repo.
GitHub is a website that hosts copies of git repos, so that you and other people can access the same repo from anywhere.

A repo hosted somewhere other than your own machine is called a 'remote'.
GitHub is just one place to host a remote - there's also Gitlab, Bitbucket, or even a server you set up yourself. We'll be using GitHub since its the most commonly used one.
When you have a remote set up, you can 'push' your commits from your laptop up to it, and 'pull' other people's commits down from it to your laptop.
This is how two people can work on the same project without emailing zip files back and forth.

Make a GitHub account if you haven't already, at github.com.

## **What is GitHub Pages?**
GitHub also has a feature called GitHub Pages, which lets you host a website directly from a repo, for free.
Whatever files you put in the repo (html, css, js) get served as an actual website with a URL, no separate hosting needed.

There's a special kind of repo for this - one named exactly `<your-username>.github.io`.
If you make a repo with that exact name, GitHub automatically treats it as your personal site, and it will be live at `https://<your-username>.github.io`.
You can also make GitHub Pages sites out of other, normal repos for individual projects, but we'll stick to the personal one since its simpler to start with, and honestly kind of exciting to have your own little corner of the internet.

We're going to use this as our example for the rest of this session to learn branching, merging and remotes. Think of it like blog2.0 - same idea as before, except now its an actual website, and now other people (like the person sitting next to you) can work on it too.

## **Setting up our page**
Go to GitHub, and make a new repository.
Name it `<your-username>.github.io`, replacing it with your actual username. So if your username was 'tushar123', you'd name the repo 'tushar123.github.io'.
Keep it public, and toggle 'Add a README' on.

Now let's bring this repo down onto our laptop. Until now we made a folder and ran `git init` inside it. This time, since the repo already exists on GitHub, we're going to 'clone' it instead - which just means copying an existing remote repo onto your machine, .git folder and all.

```bash
cd <to your preferred folder>
git clone https://github.com/<your-username>/<your-username>.github.io.git
cd <your-username>.github.io
ls -a
```
```bash
.  ..  .git  README.md
```

Notice we already have a `.git` folder and a commit in our history, without ever running `git init` ourselves. Cloning does that for us.
Let's check something new:
```bash
git remote -v
```
```bash
origin  https://github.com/<your-username>/<your-username>.github.io.git (fetch)
origin  https://github.com/<your-username>/<your-username>.github.io.git (push)
```

'origin' is just a nickname git gives to the remote you cloned from. You'll refer to it by this name from now on instead of typing the whole URL every time.
You can have multiple remotes with different names, but for now we only have the one.

Let's put an actual page in here.
```bash
echo "<h1>Hi, my name is Tushar</h1>" >> index.html
git add index.html
git commit -m "Add homepage"
```

Now go to your repo's Settings tab on GitHub, then Pages on the sidebar. Under 'Build and deployment', set Source to 'Deploy from a branch' and pick the `main` (or `master`) branch.
This tells GitHub which branch's contents to actually publish as your website.
We haven't pushed anything yet though, so let's do that.

```bash
git push origin master
```

## **Remotes, Push and Pull**
We already pushed once earlier without really explaining it, let's slow down on that now.
- `git push <remote> <branch>` uploads your local commits on that branch to the remote.
- `git pull <remote> <branch>` downloads commits from the remote and merges them into your current branch, in one go.
- `git fetch <remote>` downloads commits from the remote but does NOT merge them into anything yet. It just updates git's knowledge of what's out there.

Pull is really just fetch followed by merge. Fetch is the safer option when you just want to look at what changed before you decide to bring it into your own branch.

Let's push everything we've done so far.
```bash
git checkout master
git push origin master
```

Delete your leftover branches locally now that they're merged in, just to keep things tidy.
```bash
git branch -d about-page
git branch -d nav-links
git branch -d change-title
```

## **Working with a partner**
Time to actually use this remotely, with the person sitting next to you.
Go to your repo's Settings, then Collaborators, and add your partner's GitHub username. They'll get an invite - have them accept it.

Now, on your partner's laptop, have them clone your repo.
```bash
git clone https://github.com/<your-username>/<your-username>.github.io.git
```
Have your partner do this:
1. Make a new branch, name it something like `partner-edit`.
2. Add a small change - a new line, a new small file, whatever.
3. Add, commit it.
4. Push their branch up. Since its a new branch that doesn't exist on the remote yet, they'll need to run
```bash
git push origin partner-edit
```

Now on YOUR laptop, run
```bash
git fetch origin
git branch -a
```
You should see their `partner-edit` branch listed under remotes, even though you never made it yourself. Fetch just pulled down the information about it, it hasn't touched your working directory yet.

Check it out and have a look.
```bash
git checkout partner-edit
```
Notice this worked even though the branch only existed on the remote a second ago. Git set up a local branch tracking the remote one automatically.

If you're happy with the change, merge it into your master like we did before.
```bash
git checkout master
git merge partner-edit
git push origin master
```

You've just collaborated on a real repo with someone else, the exact same way real projects get built. Everything else we did in part 1 and today still applies - you're just doing it on a shared graph now instead of one only you can see.

## **Exercise**
Do this with your partner, swapping roles halfway through.
1. Both of you clone the same `.github.io` repo (only one of you actually owns it, the other should be added as a collaborator).
2. Each of you make a separate branch off master, and each add a different new page (e.g. `projects.html`, `contact.html`).
3. Both push your branches to the remote.
4. The repo owner fetches both branches, checks them out one at a time to look at them, then merges both into master one after another.
5. If there's a conflict (there might be, if you both touched index.html to link to your new pages), resolve it together and figure out why it happened.
6. Push master, and pull it down on the other laptop so you're both fully in sync.
7. Visit the live site and see both pages up there.
