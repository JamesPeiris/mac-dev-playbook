<img src="./docs/images/Mac-Dev-Playbook-Logo.png" width="250" height="156" alt="Mac Dev Playbook Logo" />

# Ansible Playbook | Mac Development

This playbook installs and configures most of the software I use on my Mac for software development.

Some things in macOS are slightly difficult to automate, so there are still some manual installation steps - these have all been documented.

## Installation

  1. Ensure Apple's command line tools are installed
     `xcode-select --install`
  2. [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html)
     TODO: Update these JP!

     1. Run the following command to add Python 3 to your $PATH: `export PATH="$HOME/Library/Python/3.8/bin:/opt/homebrew/bin:$PATH"`
     2. Upgrade Pip: `sudo pip3 install --upgrade pip`
     3. Install Ansible: `pip3 install ansible`

  3. Clone or download this repository to your local drive.
  4. Run `ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.
  5. Run `ansible-playbook main.yml --ask-become-pass` inside this directory. Enter your macOS account password when prompted for the 'BECOME' password.

> Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run `brew doctor` to see if this is the case.

### Use with a remote Mac

You can use this playbook to manage other Macs as well; the playbook doesn't even need to be run from a Mac at all! If you want to manage a remote Mac, either another Mac on your network, or a hosted Mac like the ones from [MacStadium](https://www.macstadium.com), you just need to make sure you can connect to it with SSH:

  1. (On the Mac you want to connect to:) Go to System Preferences > Sharing.
  2. Enable 'Remote Login'.

> You can also enable remote login on the command line:
>
>     sudo systemsetup -setremotelogin on

Then edit the `inventory` file in this repository and change the line that starts with `127.0.0.1` to:

```
[ip address or hostname of mac]  ansible_user=[mac ssh username]
```

If you need to supply an SSH password (if you don't use SSH keys), make sure to pass the `--ask-pass` parameter to the `ansible-playbook` command.

### Running a specific set of tagged tasks

You can filter which part of the provisioning process to run by specifying a set of tags using `ansible-playbook`'s `--tags` flag. The tags available are `dotfiles`, `homebrew`, `mas`, `extra-packages` and `osx`.

    ansible-playbook main.yml -K --tags "dotfiles,homebrew"

## Overriding Defaults

Not everyone's development environment and preferred software configuration is the same.

You can override any of the defaults configured in `config.defaults.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

```yaml
homebrew_installed_packages:
  - go
  - git

mas_installed_apps:
  - { id: 443987910, name: "1Password" }
  - { id: 497799835, name: "Xcode" }

configure_dock: true

dockitems_remove:
  - Launchpad
  - TV

dockitems_persist:
  - name: "Sublime Text"
    path: "/Applications/Sublime Text.app/"
    pos: 5
```

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

My [dotfiles](https://github.com/JamesPeiris/dotfiles) are also installed into the current user's home directory, including the top-level `.osx` dotfile and the various subordinate(?) dotfiles included from within this file.

These dotfiles configure many aspects of macOS for better performance and ease of use.
Management of these dotfiles can be disabled by setting `configure_dotfiles: no` in the overriding configuration.

Finally, there are a few other preferences and settings added on for various apps and services.

## Future additions

### Things that still need to be done manually

It's my hope that I can get the rest of these things wrapped up into Ansible playbooks soon, but for now, these steps need to be completed manually (assuming you already have Xcode and Ansible installed, and have run this playbook).

  1. Install all the apps that aren't yet in this setup (see below).
  2. Remap Caps Lock to Escape (requires macOS Sierra 10.12.1+).
  3. Set trackpad tracking rate.
  4. Set mouse tracking rate.
  5. Configure extra Mail and/or Calendar accounts (e.g. Google, Exchange, etc.).

## Prior Art

This project builds on the work by [Jeff Geerling](https://www.jeffgeerling.com/), specifically the [Mac Development Ansible Playbook](https://github.com/geerlingguy/mac-dev-playbook).

## TODO

- Install vscode plugins automatically
  - Go
  - Docker
  - GitLens
  - HashiCorp Terraform
  - Graphviz Interactive Preview
  - Markdown All in One
  - Markdown Preview Github Styling
  - Quokka.js
  - Makefile Tools
  - vscode-icons (TODO: confirm if this requires nerdfonts to be installed first)

- Set vscode default config
