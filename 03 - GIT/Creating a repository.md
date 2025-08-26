# Create and connect a local repo with GitHub
## How to create a git repository (windows)

- Go to the folder where you want to create the repository.
- Shift + right click → “Open in terminal”
- Create the repository
```bash
git init
```

**Expected output:**
```bash
Initialized empty Git repository in B:/ObsidianVault/.git/
```

- Create and setup a `.gitignore` file **(Optional)**

## Create a GitHub repository

- In your GitHub profile, click on the “Repositories” tab, then on the “new button”
- Fill up the Name and description, then “Create repository”

## Connect and push changes to GitHub (via HTTPS)

- Add the remote origin to our local repo
```bash
git remote add origin https://github.com/<Your-Profile>/<Repo-Name>.git
```
- Then add the files to commit with:
```bash
git add .
```
- Commit and push the changes:
```bash
git commit -m "<Your commit here>"
git push -u origin master
```

**Expected output:**
```bash
info: please complete authentication in your browser...
...
remote: Resolving deltas: 100% (1/1), done.
To https://github.com/<Your-Profile>/<Repo-Name>.git
 * [new branch]      master -> master
branch 'master' set up to track 'origin/master'.
```

- After reloading the GitHub page you should be able to see al the files pushed to the Repository
### Notes
If is the first time pushing changes to GitHub, you may get a pop-up to log in with the browser, just click “login with browser” and that should be it