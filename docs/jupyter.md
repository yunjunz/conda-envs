## Run Jupyter Lab or Notebook on a remote server

Link: https://docs.anaconda.com/anaconda/user-guide/tasks/remote-jupyter-notebook/

#### 1. On the remote server, run

```bash
# start a screen session (to keep it open for a long time)
cd ~; screen -S julab -L -Logfile julab.log

# load the python environment
load_insar

# launch a jupyter instance and port to a specific random number (NOTE: This **MUST BE DIFFERENT** for each user)
jupyter lab --no-browser --port=1236

# Ctrl-A D or Ctrl-A Ctrl-D to detach from the screen session
```

#### 2. On your laptop, run in the terminal:

```bash
# <REMOTE_USER>@<REMOTE_HOST> could also be the host profile name in your ~/.ssh/config file
ssh -N -f -L 8080:localhost:1236 <REMOTE_USER>@<REMOTE_HOST>
```

where `-N` is for not executing a remote command and just forwarding ports, the `-f` option requests SSH to go to the background, and 8080 is the local port number. 

#### 3. On your laptop, open web browser:

Then go to the following web address on your web browser: http://localhost:8080/lab. On the first time, you may be asked for a token, it's in the output of the jupyter lab command in the screen session.

#### Tips:

1. Bookmark the web address `http://localhost:8080/lab` on your browser to open the jupyter session with one click.
2. Create an alias in the `~/.bash_profile` file to re-run the `ssh` command whenever the port connection is lost.

```bash
alias julab_kunlun='ssh -N -f -L 8080:localhost:1236 host_kunlun'
```

3. Add the following in your jupyter notebook to detect and switch between different platforms:

```python
import platform

if platform.system() == 'Linux':
    proj_dir = os.path.expanduser('~/data/2023TurkeyEQ')             # on linux server
else:
    proj_dir = os.path.expanduser('~/data/archives/2023TurkeyEQ')    # on macos/windows laptop
```
