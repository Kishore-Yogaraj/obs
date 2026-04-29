
```bash
# Navigate to your project directory 
cd ~/TUHH_NN/LSTM 

# Initialize a new Git repository 
git init

# Create .gitignore file
touch or nano .gitignore

# Ignore these specific folders - add these lines to the .gitignore file
Test_Group/
Test_Group1/

# Ignore a specific file in any directory
filename.ext

# Ignore a specific file in a specific path
path/to/filename.ext

# Ignore all files with a specific extension
*.log       # Ignores all .log files
*.pyc       # Ignores all .pyc files

# Ignore files with specific names anywhere in the project
secret.txt
config.ini

# Ignore specific file types in a directory
build/*.txt

# Common Python-related ignored files
__pycache__/
*.py[cod]
*$py.class

# Virtual environment
venv/
env/
.venv/

# IDE-specific files
.vscode/
.idea/

# Jupyter Notebook
.ipynb_checkpoints/

# Logs and databases
*.log
*.sqlite3
```

3. Save the .gitignore file
```bash
# Create the .gitignore file
nano .gitignore
# Or use your preferred text editor to create and save the file
```

4. Add files to the repository
```bash
# Stage all files except those in .gitignore
git add .

# Commit the files
git commit -m "Initial commit of LSTM project"
```

5. Create a new repository on GitHub
- Go to GitHub.com and log in
- Click the "+" icon in the top right corner
- Select "New repository"
- Name it (e.g., "LSTM")
- Do NOT initialize with a README, .gitignore, or license
- Click "Create repository"

6. Link local repository to GitHub
```bash
# Add the GitHub repository as a remote
git remote add origin https://github.com/YOUR_USERNAME/LSTM.git

# Verify the remote
git remote -v

# Push your code to GitHub
git push -u origin master
```

Additional Tips:
- Replace `YOUR_USERNAME` with your actual GitHub username
- The first time you push, you might need to authenticate
- If using HTTPS, you'll be prompted for GitHub credentials
- If using SSH, ensure you've set up SSH keys with GitHub