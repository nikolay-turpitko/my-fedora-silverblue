# Custom installation of `Fedora Silverblue`

TL;DR; This project contains custom anaconda kickstarter configuration,
post-installation scripts, personal configuration files and instructions to
automate initial setup of Fedora Silverblue with Sway, Gopass and my
personal preferences and personal projects. You may use it on your own
discretion, but check everything twice.

## Using kickstarter file during installation

To use kickstarter preset file, it should be available for target system.
See https://docs.fedoraproject.org/en-US/fedora/rawhide/install-guide/advanced/Boot_Options/ for details.
During the boot menu of the installer (on UEFI) use `e` to edit boot options.

    # Run next command on the host in the folder with anaconda-ks.cfg.
    # This command will serve dir's content on 8000 port.
    python3 -m http.server

    # Add next option to boot options of the installer (example for virtualbox).
    inst.ks=http://10.0.2.2:8000/anaconda-ks.cfg

## Manually check installation options

My personal settings:

- Use `English (US)` for installation and as main locale.
- Add `Russian` keyboard layout and use `Right Alt` as switcher.
- Check that `English (US)` is a default layout.
- Add `Russian` language support, besides `English (US)`.
- Set timezone to `Azia/Novosibirsk`.
- Check partitioning options:
  - Use manual option.
  - Set regular variant (no LVM, etc).
  - Let it add partitions automatically, then adjust sizes.
  - Remove root and home partitions and add single root partition using the whole available size.
  - Enable encryption of the root partition and enter passphrase after pressing `Done` button.
  - Apply changes.

## After installation

- Copy content of this folder to target machine using scp or git (`scp -r nick@10.0.2.2:/home/nick/Projects/my-fedora-silverblue ~/Downloads`).
- Run `postinst-*` scripts, rebooting between them as requested.

## Other manual steps after installation

1. Restore from backup your keys and passwords into:

  - `.ssh`;
  - `.gnupg` (don't overwrite `gpg-agent.conf` copied at steps above);

   These keys are required at the next steps. Particularly, to access private
   repositories, or to clone owned repositories with write privileges. 
    
   You may skip this step and change `~/.mrconfig` to use `https://` schema for
   read-only access. It may be enough for temporary VM installations, if you do
   not plan to make backups of dot files from this system, or if you want to
   manually setup remote servers for each repository later.

   Sample command to restore parts from tar backup:

        sudo tar -C /home/nick \
                 --strip-components=3 \
                 -xvf /backup/tar/2018-04-05/vbx-files.tar.gz \
                 files/home/nick/.ssh \
                 files/home/nick/.gnupg

2. Check and amend your repository settings in `~/.mrconfig` file. Put there
   URLs of your own forks or repositories (you may have no access to mine).

        vim ~/.mrconfig

3. Update dot files and other projects from remote repositories.  Don't forget
   `ssh-add`, or you'll have to enter password many times.

        cd ~
        ssh-add
        mr update

4. I decided to skip making symlinks on configuration files with `stow` on my
   fedora installation for now. Replace `.mrconfig` with private version, amend
   it manually.

5. Depending on your workflow, you may need to update all repositories again
   after previous steps. For example, you may replace bootstrapped copy of 
   `.mrconfig` with your own, stored in the private repo. In this case, you'd
   manually edit/replace bootstrapped copy at step 2 for `mr` to checkout all
   your private repos, one of which could contain your own copy of `.mrconfig`.
   So, at step 5 you'd resolve repository conflicts and choose to use yours 
   copy of `.mrconfig`. After that, you may want to issue command to update
   you repositories again. This a bit complicated at first glance, but allows
   to keep diverged version of `.mrconfig` under private version control.

        mr update

7. Re-login (use `Alt+Shift+E` to exit `sway`).

8. Run `vim +GoInstallBinaries +"helptags ALL" +q`.

## Additional packages

### Google Cloud SDK

Repository definition should be added to `/etc/yum.repos.d` by the `postinst-*`
scripts. Cloud SDK should be available for installation. I installed it in the `toolbox`:

    toolbox create -c work
    toolbox enter -c work
    sudo dnf install google-cloud-sdk google-cloud-sdk-app-engine-go

I'm not sure if this is a good idea to do it like this. Will see.

### MySQL Workbench

See https://dev.mysql.com/downloads/repo/yum/ for fresh download link.

    cd Downloads
    wget https://dev.mysql.com/get/mysql80-community-release-fc31-1.noarch.rpm
    rpm-ostree install mysql80-community-release-fc31-1.noarch.rpm
    reboot
    rpm-ostree install mysql-workbench

