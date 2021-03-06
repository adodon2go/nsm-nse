NAME = vl3-nse
CONTAINERS = vl3-nse
PODS = vl3-nse
CHECK = export ITERATIONS=10 && export BATCHES=1 && scripts/check_vl3.sh

VPP_AGENT_BASE_IMAGE = ligato/vpp-agent:v3.2.0

include $(TOP)/build/targets.mk

# If GOPATH is made up of several paths, use the first one for our targets in this Makefile
GO_TOP := $(shell echo ${GOPATH} | cut -d ':' -f1)
export GO_TOP
export OUT_DIR=$(GO_TOP)/out
BUILDTYPE_DIR:=debug
export VL3_OUT_LINUX:=$(OUT_DIR)/linux_amd64/$(BUILDTYPE_DIR)
# scratch dir for building isolated images
DOCKER_BUILD_TOP:=${VL3_OUT_LINUX}/docker_build

.PHONY: build-vl3
build-vl3:
	GO111MODULE=on CGO_ENABLED=0 GOOS=linux go build -ldflags '-extldflags "-static"' -o ${GOPATH}/bin/linux_amd64/vl3_nse ./cmd/vl3-nse/...

.PHONY: docker-vl3
docker-vl3: build-vl3
	mkdir -p ${DOCKER_BUILD_TOP}/vl3/etc
	cp -p ./build/nse/vl3-nse/Dockerfile.no_go_build_ucnf ${DOCKER_BUILD_TOP}/vl3/
	cp -p ${GOPATH}/bin/linux_amd64/vl3_nse ${DOCKER_BUILD_TOP}/vl3/
	cp -pr ./build/nse/universal-cnf/vppagent/etc/* ${DOCKER_BUILD_TOP}/vl3/etc/
	cd ${DOCKER_BUILD_TOP}/vl3 && docker build -t ${ORG}/vl3_ucnf-nse:${TAG} --build-arg VPP_AGENT=${VPP_AGENT_BASE_IMAGE} -f Dockerfile.no_go_build_ucnf .
