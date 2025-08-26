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