Flatpak
=======

  * Project [Flatpak](http://flatpak.org/)
  * Automated runtime build; [GitLab](https://gitlab.com/gbraad/flatpak-runtime-build)


Installation
------------

### Runtime
```
$ dnf install -y flatpak
$ wget https://sdk.gnome.org/keys/gnome-sdk.gpg -o /tmp/gnome-sdk.gpg
$ flatpak remote-add --gpg-import=/tmp/gnome-sdk.gpg gnome https://sdk.gnome.org/repo/
$ flatpak remote-add --gpg-import=/tmp/gnome-sdk.gpg gnome-apps https://sdk.gnome.org/repo-apps/
$ flatpak install gnome org.gnome.Platform 3.20
```

### Application
```
$ flatpak install gnome-apps org.gnome.gedit stable
$ flatpak run org.gnome.gedit
```

### SDK
```
$ flatpak install gnome org.gnome.Sdk 3.20
```
