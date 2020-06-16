GOFLAGS =
LDFLAGS =

BINDIR := $(CURDIR)/bin

BC2PGSQL_BINNAME ?= bc2pgsql
BC2PGSQL_SRCDIR  ?= ./cmd/bc2pgsql
BC2PGSQL_SRC     := $(shell find $(BC2PGSQL_SRCDIR) -type f -name '*.go' -print)

GIT_BRANCH = $(shell git rev-parse --abbrev-ref HEAD)
ifeq ($(GIT_BRANCH),)
GIT_BRANCH = "none"
endif

GIT_COMMIT = $(shell git rev-parse HEAD)
ifeq ($(GIT_COMMIT),)
GIT_COMMIT = "0000000000000000000000000000000000000000"
endif

GIT_DIRTY = $(shell test -n "`git status --porcelain`" && echo true || echo false)
ifeq ($(GIT_DIRTY),true)
GIT_TREE_STATE = "dirty"
else
GIT_TREE_STATE = "clean"
endif

GIT_SHA = $(shell git rev-parse --short HEAD)
ifeq ($(GIT_SHA),)
GIT_SHA=0000000
endif

GIT_TAG = $(shell git describe --tags --abbrev=0 2>/dev/null)
ifeq ($(GIT_TAG),)
GIT_TAG=none
endif

VERSION_MAJOR = $(shell echo ${GIT_TAG} | cut -d '.' -f 1 | tr -d -c 0-9)
ifeq ($(VERSION_MAJOR),)
VERSION_MAJOR=0
endif

VERSION_MINOR = $(shell echo ${GIT_TAG} | cut -d '.' -f 2 | tr -d -c 0-9)
ifeq ($(VERSION_MINOR),)
VERSION_MINOR=0
endif

VERSION_PATCH = $(shell echo ${GIT_TAG} | cut -d '.' -f 3 | tr -d -c 0-9)
ifeq ($(VERSION_PATCH),)
VERSION_PATCH=0
endif

LDFLAGS += -X main.AppName=${BC2PGSQL_BINNAME}
LDFLAGS += -X main.VersionBranch=${GIT_BRANCH}
LDFLAGS += -X main.VersionSuffix=${GIT_SHA}
LDFLAGS += -X main.VersionDirty=${GIT_DIRTY}
LDFLAGS += -X main.VersionMajor=${VERSION_MAJOR}
LDFLAGS += -X main.VersionMinor=${VERSION_MINOR}
LDFLAGS += -X main.VersionPatch=${VERSION_PATCH}

.PHONY: all
all: info build

.PHONY: build
build: build-bc2pgsql

.PHONY: build-bc2pgsql
build-bc2pgsql: $(BINDIR)/$(BC2PGSQL_BINNAME)
$(BINDIR)/$(BC2PGSQL_BINNAME): $(BC2PGSQL_SRC)
	cd ${BC2PGSQL_SRCDIR} && \
		GO111MODULE=on go build $(GOFLAGS) -ldflags '$(LDFLAGS)' -o $(BINDIR)/$(BC2PGSQL_BINNAME)

.PHONY: clean
clean:
	@echo "Cleaning up..."
	rm -rf bin

.PHONY: fmt
fmt:
	@echo "Formating..."
	gofmt -l -w main.go

.PHONY: info
info:
	@echo "Version info:"
	@echo "  - Git Branch     :  ${GIT_BRANCH}"
	@echo "  - Git Tag        :  ${GIT_TAG}"
	@echo "  - Git Commit     :  ${GIT_COMMIT}"
	@echo "  - Git Tree State :  ${GIT_TREE_STATE}"
	@echo "  - Version Major  :  ${VERSION_MAJOR}"
	@echo "  - Version Minor  :  ${VERSION_MINOR}"
	@echo "  - Version Patch  :  ${VERSION_PATCH}"

.PHONY: mod-init
mod-init:
	@echo "Initializing new go modules..."
	@rm -f go.mod go.sum && \
		go mod init github.com/NuLL3rr0r/bc2pgsql
	cd cmd/bc2pgsql && \
		rm -f go.mod go.sum && \
		go mod init github.com/NuLL3rr0r/bc2pgsql/cmd/bc2pgsql && \
		GO111MODULE=on go get

.PHONY: mod-tidy
mod-tidy:
	@echo "Cleanup stale dependencies..."
	GO111MODULE=on go mod tidy
	cd cmd/bc2pgsql && \
		GO111MODULE=on go mod tidy

.PHONY: mod-vendor
mod-vendor:
	@echo "Vendoring..."
	@rm -rf vendor && \
		go mod vendor
	cd cmd/bc2pgsql && \
		rm -rf vendor && \
		go mod vendor ;
