# VSCode setup to access VM over SSH

1. Add the following lines to `/home/$USERNAME/.ssh/config` in your host machine:

```bash
Host cs323vm
  HostName 127.0.0.1
  User cs323
  Port 2222
```

2. Install the [Remote-SSH Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).

3. In VS Code, select `Remote-SSH: Connect to Host...` from the Command Palette (F1, Ctrl+Shift+P) and use `cs323vm`.

4. Enter VM password: `cs323`.

You should now be able to access all the files and run the commands inside the VM.
