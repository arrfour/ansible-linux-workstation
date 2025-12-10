# ansible-linux-workstation

Ansible config for my Linux workstation setup.  This is currently geared to a Windows Subsystem for Linux (WSL) setup but could be easily adapted.  Some WSL specific settings include creation of wsl.conf file and creating a symlink to the kube config in Windows so it can be shared.

The playbook includes automated installation of:
- Base packages (curl, wget, git, jq, etc.)
- HashiCorp tools (Terraform, Vault)
- Python tools and packages
- PowerShell
- Kubernetes tools (kubectl, helm, kind, k9s, etc.)
- Azure CLI
- ZSH with Oh-My-Zsh and customizations
- **Dotfiles** (bash, vim, tmux configurations from https://github.com/arrfour/dotfiles)

The username is automatically detected from your environment variables. Update these if needed by passing `-e` flags when running the playbook.

Because the kube-tools tasks create a symlink to a Windows path (for example `/mnt/c/Users/...`) which may not be available until the WSL distro has been restarted, the playbook can fail on the first run. Restart your WSL distro before running again — discover distro names with `wsl -l -v` and terminate the distro with:

```
wsl --terminate <distro>
```

Example: run the playbook with overrides for your Linux username and Windows user path:

```
sudo ansible-playbook playbook/playbook.yaml -e "user=arr4 user_windows_path=/mnt/c/Users/arr4"
```

Updating pinned tool versions
-----------------------------

- The playbook pins several tool versions via variables at the top of `playbook/playbook.yaml` (e.g. `terraform_version`, `vault_version`, `kind_version`).
- To change a pinned version, edit `playbook/playbook.yaml` and update the variable value, or override it when running Ansible using `-e`. Example:

```pwsh
sudo ansible-playbook playbook/playbook.yaml -e "terraform_version=1.6.0 vault_version=1.15.0"
```

- Note: some tooling (helm, k9s, octant) currently have version strings inside `playbook/kube-tools.yaml`. If you plan to bump those, either update the literal strings in that file or let me know and I can expose them as variables in `playbook/playbook.yaml` for easier management.

1. Install Ansible

    ```
    sudo apt update
    sudo apt install software-properties-common
    sudo apt-add-repository --yes --update ppa:ansible/ansible
    sudo apt install ansible
    ```

2. Run playbook

    ```
    sudo ansible-playbook playbook/playbook.yaml
    ```

For all of the zsh customizations to display properly you will need to install new fonts in Windows and update your shell or code editor accordingly.  See:  
<https://gist.github.com/PashCracken/b6070359486ea651eed66a5e86567ebb#install-required-nerd-fonts>

<https://www.nerdfonts.com/>

<https://github.com/ryanoasis/nerd-fonts/releases>

# Resources

- <https://gist.github.com/PashCracken/b6070359486ea651eed66a5e86567ebb>
- <https://itnext.io/setting-up-the-kubernetes-tooling-on-windows-10-wsl-d852ddc6699c>
- <https://www.edwardthomson.com/blog/git_credential_manager_with_windows_subsystem_for_linux.html>
