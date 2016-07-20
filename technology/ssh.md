ssh
===


## Resume partial download
If a transfer failed using `scp`, you can resume using `rsync`

```
rsync --partial --progress --rsh=ssh "[user]@[host]:[path]" [localfile] 
```
