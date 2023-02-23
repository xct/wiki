# Misc

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

