Flatpak
=======

  * Project [Flatpak](http://flatpak.org/)
  * [Getting Started](http://flatpak.org/developer.html)


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


Builders
--------

  * GNOME Runtime and SDK: [GitLab](https://gitlab.com/gbraad/flatpak-builder-gnome), [GitHub](https://github.com/gbraad/docker-flatpak-builder-gnome)  
    `docker run -it --rm -v $PWD:/workspace registry.gitlab.com/gbraad/flatpak-builder-gnome bash`
  * freedesktop Runtime and SDK: [GitLab](https://gitlab.com/gbraad/flatpak-builder-freedesktop), [GitHub](https://github.com/gbraad/docker-flatpak-builder-freedesktop)  
    `docker run -it --rm -v $PWD:/workspace registry.gitlab.com/gbraad/flatpak-builder-freedesktop bash`


Runtimes
--------

  * Official [runtimes](http://flatpak.org/runtimes.html)
  * Automated runtime build; [GitLab](https://gitlab.com/gbraad/flatpak-runtime-build)
  * CentOS: https://github.com/matthiasclasen/flatpak-runtime
  * openSUSE: https://github.com/fcrozat/opensuse-flatpak-runtime
  * Mono: (WIP!): https://gitlab.com/gbraad/flatpak-mono-runtime, https://github.com/gbraad/flatpak-mono-runtime
  * Fedora (WIP!): https://gitlab.com/gbraad/flatpak-fedora-runtime, https://github.com/gbraad/flatpak-fedora-runtime


Applications
------------

  * Spotify build wrapper: [GitLab](https://gitlab.com/gbraad/flatpak-spotify-build)
  * Hello app: [GitLab](https://gitlab.com/gbraad/flatpak-hello)
  * OpenStack (WIP!) https://github.com/gbraad/flatpak-openstack-client


Test
----

  * Test container: [GitHub](https://github.com/gbraad/docker-flatpak), [GitLab](https://gitlab.com/gbraad/flatpak)
