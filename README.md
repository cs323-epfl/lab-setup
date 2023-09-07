# Tools setup - CS323

1. Download [VirtualBox](https://www.virtualbox.org/wiki/Downloads).
2. Download the image [cs323-OS.ova.xz]().
3. Extract the image using the following command:
	`unxz cs323-OS.ova.xz`
4. In VirtualBox do File→Import Appliance, select the image and finish it by iterating on next button.
3. Start the machine called `cs323-OS`.
4. There is an already created user `cs323` with password `cs323`.
5. There is a predefined port forwarding from your host port 2222 to the VM port 22. Hence, you don't need to work in graphical mode and can SSH into the VM with `ssh -p 2222 cs323@localhost`.

7. Within the VM, open the terminal either via SSH or GUI.
8. Follow steps to [Generate SSH keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).
9. Follow steps to [Add your keys to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).
10. Follow steps to [Test your SSH connection](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection).
11. VM is setup. You can clone your assignment repo and start working on it.

If you are paranoid about using preinstalled system, you can install Ubuntu 22.04 (x86-4) on your own and check [post_install.sh](https://gitlab.epfl.ch/cs323/vm/-/blob/main/post_install.sh) to see what was done from our side.



