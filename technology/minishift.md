Minishift
=========


Build environment
-----------------

```bash
#/bin/sh
OS=linux
ARCH=amd64
GO_VERSION=1.7.1
GO_RELEASE_URL="https://storage.googleapis.com/golang/go${GO_VERSION}.${OS}-${ARCH}.tar.gz"
curl ${GO_RELEASE_URL} -o /tmp/go.tar.gz
mkdir -p /opt/go
tar --directory=/opt/go -xvf /tmp/go.tar.gz
#rm /tmp/go.tar.gz
export PATH=$PATH:/opt/go/bin

GLIDE_OS_ARCH=`go env GOHOSTOS`-`go env GOHOSTARCH`
GLIDE_TAG=$(curl -s https://glide.sh/version)
GLIDE_LATEST_RELEASE_URL="https://github.com/Masterminds/glide/releases/download/${GLIDE_TAG}/glide-${GLIDE_TAG}-${GLIDE_OS_ARCH}.tar.gz"
wget ${GLIDE_LATEST_RELEASE_URL} -o /tmp/glide.tar.gz
mkdir -p /opt/glide
tar --directory=/opt/glide -xvf /tmp/glide.tar.gz
#rm /tmp/glide.tar.gz
export PATH=$PATH:/opt/glide/${GLIDE_OS_ARCH}

GOPATH=$PWD/_gopath
glide install -v
make
```