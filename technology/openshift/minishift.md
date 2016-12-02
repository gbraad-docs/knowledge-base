Minishift
=========


Build environment
-----------------

```bash
#!/bin/sh
OS=linux
ARCH=amd64
GO_VERSION=1.7.1
GO_RELEASE_URL="https://storage.googleapis.com/golang/go${GO_VERSION}.${OS}-${ARCH}.tar.gz"
curl -sSL ${GO_RELEASE_URL} -o /tmp/go.tar.gz
GO_TARGET=$HOME
mkdir -p ${GO_TARGET}/go
tar --directory=${GO_TARGET} -xvf /tmp/go.tar.gz
#rm /tmp/go.tar.gz
export GOROOT=${GO_TARGET}/go/
export PATH=$PATH:${GO_TARGET}/go/bin

GLIDE_OS_ARCH=`go env GOHOSTOS`-`go env GOHOSTARCH`
GLIDE_TAG=$(curl -s https://glide.sh/version)
GLIDE_LATEST_RELEASE_URL="https://github.com/Masterminds/glide/releases/download/${GLIDE_TAG}/glide-${GLIDE_TAG}-${GLIDE_OS_ARCH}.tar.gz"
GLIDE_TARGET=$HOME
curl -sSL ${GLIDE_LATEST_RELEASE_URL} -o /tmp/glide.tar.gz
mkdir -p ${GLIDE_TARGET}/glide
tar --directory=${GLIDE_TARGET}/glide -xvf /tmp/glide.tar.gz
#rm /tmp/glide.tar.gz
export PATH=$PATH:${GLIDE_TARGET}/glide/${GLIDE_OS_ARCH}

GOPATH=$PWD/_gopath
glide install -v
make
```
