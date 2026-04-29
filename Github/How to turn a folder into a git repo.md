### How to turn a folder into a git repo 
```bash
git init
git add.
git commit -m "Initial commit"
#Go to github online and create a repo and do not check initialize this repo with a README
git remote add origin <https link of repo you created online>
#The above command may not work if you cloned another repo because a remote origin is already set up. You can double check by doing <git remote -v>. Instead you can replace origin in the above command with origin 2. In the below command you would also use origin2
git push -u origin main
```
