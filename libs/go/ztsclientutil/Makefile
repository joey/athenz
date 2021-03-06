#
# Makefile to build ZMS Service Token client utility
# Prerequisite: Go development environment
#
# Copyright 2018 Yahoo Holdings, Inc.
# Licensed under the Apache License, Version 2.0 - http://www.apache.org/licenses/LICENSE-2.0
#

GOPKGNAME = github.com/yahoo/athenz/libs/go/ztsclientutil
PKG_DATE=$(shell date '+%Y-%m-%dT%H:%M:%S')
BINARY=ztsclientutil
FMT_LOG=/tmp/ztsclientutil-fmt.log
IMPORTS_LOG=/tmp/ztsclientutil-imports.log

# check to see if go utility is installed
GO := $(shell command -v go 2> /dev/null)
export GOPATH=$(PWD)

ifdef GO

# we need to make sure we have go 1.7+
# the output for the go version command is:
# go version go1.7.3 darwin/amd64

GO_VER_GTEQ7 := $(shell expr `go version | cut -f 3 -d' ' | cut -f2 -d.` \>= 7)
ifneq "$(GO_VER_GTEQ7)" "1"
all:
	@echo "Please install 1.7.x or newer version of golang"
else

.PHONY: source vet fmt imports build
all: source vet fmt imports build

endif

else

all:
	@echo "go is not available please install golang"

endif

# we need to build the ztsclientutil with the local copy
# of zts-go-client so that any changes can be reflected
# right away. So we are going to copy the source directories
# into our source tree and compile them
source:
	@echo "Cleanup up local GOPATH src directory..."
	rm -rf src
	@echo "Setting up the source code..."
	mkdir -p /tmp/ztsclientutil-build/src/$(GOPKGNAME)
	cp -r * /tmp/ztsclientutil-build/src/$(GOPKGNAME)/.
	mv /tmp/ztsclientutil-build/src .
	@echo "Copying local source files..."
	mkdir -p $(GOPATH)/src/github.com/yahoo/athenz/clients/go
	cp -r ../../../clients/go/zts $(GOPATH)/src/github.com/yahoo/athenz/clients/go
	@echo "Getting dependency packages..."
	go get -t -d -tags testing $(GOPKGNAME)/...

vet:
	go vet $(GOPKGNAME)/...

# we are going to verify our library
fmt:
	go list $(GOPKGNAME)/... | sed "s:^:$(GOPATH)/src/:" | xargs gofmt -d >$(FMT_LOG)
	@if [ -s $(FMT_LOG) ]; then echo gofmt FAIL; cat $(FMT_LOG); false; fi

# we are going to verify our library
imports:
	go get golang.org/x/tools/cmd/goimports
	go list $(GOPKGNAME)/... | sed "s:^:$(GOPATH)/src/:" | xargs $(GOPATH)/bin/goimports -d >$(IMPORTS_LOG)
	@if [ -s $(IMPORTS_LOG) ]; then echo goimports FAIL; cat $(IMPORTS_LOG); false; fi

build:
	@echo "Building ztsclientutil library..."
	go install -v $(GOPKGNAME)/...

clean:
	rm -rf target src bin pkg /tmp/ztsclientutil-build $(FMT_LOG) $(IMPORTS_LOG)
