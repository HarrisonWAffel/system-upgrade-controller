ARG BCI=registry.suse.com/bci/bci-base:15.6
ARG GOLANG=registry.suse.com/bci/golang:1.22

FROM ${GOLANG} AS e2e-ginkgo
ENV GOBIN=/bin
RUN go install github.com/onsi/ginkgo/v2/ginkgo@v2.20.0

FROM ${BCI} AS e2e-tests
ARG TARGETARCH
ARG REPO=rancher
ARG TAG
ENV SYSTEM_UPGRADE_CONTROLLER_IMAGE=${REPO}/system-upgrade-controller:${TAG}
COPY --from=e2e-ginkgo /bin/ginkgo /bin/ginkgo
COPY dist/artifacts/system-upgrade-controller.test-${TARGETARCH} /bin/system-upgrade-controller.test
COPY e2e/plugin/run.sh /run.sh
RUN set -x \
    && chmod +x /run.sh
RUN set -x \
    && zypper -n in tar gzip
ENTRYPOINT ["/run.sh"]

FROM scratch AS controller
ARG TARGETARCH
COPY dist/artifacts/system-upgrade-controller-${TARGETARCH} /bin/system-upgrade-controller
USER 65534:65534
ENTRYPOINT ["/bin/system-upgrade-controller"]
