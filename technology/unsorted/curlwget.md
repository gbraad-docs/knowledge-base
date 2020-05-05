Curl and wget
=============


## Download files from website path

```
$ wget \
     --recursive \
     --no-clobber \
     --page-requisites \
     --html-extension \
     --convert-links \
     --restrict-file-names=windows \
     --domains website.org \
     --no-parent \
         www.website.org/files/
```


## Silent mode

### `curl`
```
$ curl --silent 
```

### `wget`
```
$ wget --quiet
```
