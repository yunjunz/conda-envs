## Run Jupyter Lab or Notebook on a remote server

Link: https://docs.anaconda.com/anaconda/user-guide/tasks/remote-jupyter-notebook/

#### 1. On the remote server

```bash
# start a screen session (to keep it open for a long time)
cd ~; screen -S julab -L -Logfile julab.log

# load the python environment
load_insar

# launch a jupyter instance and port to a specific random number
# NOTE: this port number **MUST BE DIFFERENT** for each user
jupyter lab --no-browser --port=1236

# Ctrl-A D or Ctrl-A Ctrl-D to detach from the screen session
```

#### 2. On your laptop terminal:

Create an alias in the `~/.bash_profile` file.

```bash
alias julab_kunlun='ssh -N -f -L 8080:localhost:1236 <REMOTE_USER>@<REMOTE_HOST>'
```

where `-N` is for not executing a remote command and just forwarding ports, the `-f` option requests SSH to go to the background, and 8080 is the local port number. The `<REMOTE_USER>@<REMOTE_HOST>` could also be the host profile name in your `~/.ssh/config` file, e.g. `host_kunlun`.

Then run `julab_kunlun` to forward the port connection or to re-connect whenever it's lost.

#### 3. On your laptop web browser:

Go to the web address: http://localhost:8080/lab. On the first time, you may be asked for a token, it's in the output of the jupyter lab command in the screen session. Bookmark this web address for easy re-opening.

#### Tips:

1. Add the following in your jupyter notebook to detect and switch between different platforms:

```python
import platform

if platform.system() == 'Linux':
    proj_dir = os.path.expanduser('~/data/2023TurkeyEQ')             # on linux server
else:
    proj_dir = os.path.expanduser('~/data/archives/2023TurkeyEQ')    # on macos/windows laptop
```

2. If `julab_kunlun` returns `address already in use` error, run the following to kill the process, and then re-connect.

```bash
lsof -ti:8080 | xargs kill -9
```
