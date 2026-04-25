rsync
=====


## Sync remote to local

```
$ rsync --progress [username]@[hostname]:[path]/[file(s)] [local path]
```

Note: `--progress` or `-P` show progress bar


## Resume partial download
If a transfer failed using `scp`, you can resume using `rsync`

```
rsync --partial --progress --rsh=ssh [username]@[hostname]:[path][/file(s)] [local path] 
```

Note: using `zsh` you might need to use `"` around the path specifiers (due to globbing)
