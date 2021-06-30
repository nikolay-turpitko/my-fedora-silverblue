# Upgrade fedora version 31 -> 32

First of all, I noticed that I have to upgrade after `gopass` disappeared from
the repo for v31 and I found that they, probably, discontinued to support this
version, because it was available in the v32 folder on the same repo.

So, I decided to upgrade. But during first attempts to upgrade I found 2 errors:

- transaction was not able to commit, because of absence of `gopass`;
- `rpmfusion-free-release-31` was installed and was not able to upgrade to v32;

To proceed with upgrade I had to delete conflicting packages and install them
after upgrade.

Command history:

    rpm-ostree uninstall gopass
    rpm-ostree uninstall rpmfusion-free-release-31-1.noarch rpmfusion-nonfree-release-31-1.noarch
    ostree remote list
    sudo ostree remote gpg-import fedora -k /etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-32-primary
    rpm-ostree rebase fedora:fedora/32/x86_64/silverblue
    rpm-ostree upgrade
    rpm-ostree install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-32.noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-32.noarch.rpm
    rpm-ostree install gopass
    reboot
    
    toolbox enter -c work
    sudo dnf update && sudo dnf upgrade

