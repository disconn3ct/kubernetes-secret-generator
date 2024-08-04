# Build the manager binary
FROM --platform=${BUILDARCH:-amd64} golang:1.23.1 AS builder
ARG TARGETOS TARGETARCH

WORKDIR /workdir
# ENV GOPATH=/go
# Copy the Go Modules manifests
COPY go.mod go.sum /workdir/
# cache deps before building and copying source so that we don't need to re-download as much
# and so that source changes don't invalidate our downloaded layer
RUN GOOS=$TARGETOS GOARCH=$TARGETARCH go mod download

# Copy the go source
COPY cmd cmd
COPY pkg pkg
COPY version version

# Build
RUN CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -a -v -o /workspace/manager-${TARGETARCH} ./cmd/manager/main.go

# Use distroless as minimal base image to package the manager binary
# Refer to https://github.com/GoogleContainerTools/distroless for more details
FROM gcr.io/distroless/static:nonroot
ARG TARGETOS TARGETARCH
WORKDIR /
COPY --from=builder /workspace/manager-${TARGETARCH} ./manager
USER 65532:65532

ENTRYPOINT ["/manager"]
