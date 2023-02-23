# Misc

### Docker

After suspending a VM with a running docker container and resuming it, the connection to the  container might be broken. To fix this run:

```text
systemctl restart NetworkManager docker
```

### Create pull request on Bitbucket

* Clone the master branch
* Make your changes
* Commit your changes
* `git checkout -b feature/yournewfeaturename`
* `git push --set-upstream origin feature/yournewfeaturename`
* Create the pull request in the GUI \(self explanatory\)

### Convert MKV to MP4

```text
ffmpeg -i in.mkv -codec copy out.mp4
```

### Fix Broken VMWare Workstation Briged Mode

Go to Virtual Network Editor and edit VMNet0, deactivate all adapters that might be causing issues \(only leave the one to be bridged\).

