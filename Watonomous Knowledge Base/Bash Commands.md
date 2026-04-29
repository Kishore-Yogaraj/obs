```bash
your_command & #Will run the command and give you the terminal back so that you can run other commands
```

### Starting up Dev Node For Wato
```bash
cd wato_asd_tooling #cd in home directory
nano session_config.sh #configure the node you want to build (GPU, cpu, memory, etc)
bash start_interactive_sesion.sh
```

### Checking what an ENV variable is
```bash
eccho $VARIABLE_NAME
```

### Checking disk space on slurm node
```bash
df -ah | grep /tmp #Shows you how much disk space you have and how much is avail
```

### Checking GPU stats
```bash
nvidia-smi
```

### Creating an Alias for a command you run often in terminal

```bash
nano ~/.bashrc
alias startsession='bash start_interactive_session.sh' #write in bashrc file

# Can also do this if you want to cd to directory (wato_asd_tooling) and run the command with one command. This way you can start up vscode and just enter the command
alias startsession='cd /path/to/directory && bash start_interactive_session.sh'

source ~/.bashrc #execute in terminal after making changes
```

### Check permissions on file

```bash
ls -l <full path to file> 
```