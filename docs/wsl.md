## Install Windows Subsystem for Linux

The Windows Subsystem for Linux (WSL) lets developers access the power of both Windows and Linux at the same time on a Windows machine, including Linux applications, utilities, and Bash command-line tools directly, without the overhead of a traditional virtual machine or dualboot setup.

### Option 1: Install WSL with one command

Open PowerShell by right-clicking and selecting "Run as administrator". Enter the 'wsl --install' command and restart your machine when the installation finish. See [How to install Linux on Windows with WSL](https://learn.microsoft.com/en-gb/windows/wsl/install) for more information.

```bash
wsl --install
```

### Option 2: Install WSL manually

If the installation failed due to network error, you can disable the Windows Subsystem for Linux and Virtual Machine feature and restart your machine. Or if you just prefer step by step instructions, you can also install it manually. See [Manual installation steps for older versions of WSL](https://learn.microsoft.com/en-gb/windows/wsl/install-manual) for more information. 

##### a. Enable the Windows Subsystem for Linux.

Open PowerShell by right-clicking and selecting "Run as administrator" and run:

```bash
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

##### b. Restart your machine.

##### c. Install your Linux distribution of choice, such as Ubuntu in your Mircosoft Store.

##### d. Enable Virtual Machine feature.

Open PowerShell by right-clicking and selecting "Run as administrator" and run:

```bash
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

##### e. Restart your machine.

##### f. Download the Linux kernel update package, double-click and run it.

##### g. Set WSL 2 as your default version. Open PowerShell and run:

```bash
wsl --set-default-version 2
```
