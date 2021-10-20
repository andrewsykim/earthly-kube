VERSION --no-implicit-ignore 0.5

FROM gcr.io/k8s-staging-test-infra/kubekins-e2e:v20211001-f2ebda117d-master
# FROM k8s.gcr.io/build-image/kube-cross:v1.23.0-go1.17.1-bullseye.1
WORKDIR /go/src/k8s.io/kubernetes
ARG WHAT
ARG TESTS
ARG BRANCH
ENV PATH=/go/src/k8s.io/kubernetes/third_party/etcd:${PATH}


src:
    # install any additional tools that can be cached once.
    RUN cd /tmp && curl -LO https://github.com/koalaman/shellcheck/releases/download/v0.7.1/shellcheck-v0.7.1.linux.x86_64.tar.xz && \
        tar -xf shellcheck-v0.7.1.linux.x86_64.tar.xz && \
	cp /tmp/shellcheck-v0.7.1/shellcheck /usr/local/bin
    COPY api/ api/
    COPY build/ build/
    COPY CHANGELOG/ CHANGELOG/
    COPY cluster/ cluster/
    COPY cmd/ cmd/
    COPY docs/ docs/
    COPY hack/ hack/
    COPY LICENSES/ LICENSES/
    COPY logo/ logo/
    COPY pkg/ pkg/
    COPY plugin/ plugin/
    COPY staging/ staging/
    COPY test/ test/
    COPY third_party/ third_party/
    COPY vendor/ vendor/
    COPY go.mod go.sum .generated_files .gitattributes .gitignore Earthfile LICENSE code-of-conduct.md CONTRIBUTING.md OWNERS OWNERS_ALIASES README.md SECURITY_CONTACTS SUPPORT.md ./
    COPY .git/ .git/ #  COPY .git because some build scripts use git
    COPY .github/ .github/
    RUN ln -s build/root/Makefile Makefile
    RUN ln -s build/root/Makefile.generated_files Makefile.generated_files
    RUN ln -s CHANGELOG/README.md CHANGELOG.md
    # RUN make generated_files


build:
    FROM +src
    RUN hack/make-rules/build.sh $WHAT
    SAVE ARTIFACT _output AS LOCAL _output

ginkgo:
    FROM +src
    RUN hack/make-rules/build.sh vendor/github.com/onsi/ginkgo/ginkgo
    SAVE ARTIFACT _output AS LOCAL _output


# verify is like calling "make verify" but each check is run in parallel
# as isolated earthly targets
verify:
    BUILD +verify-api-groups
    BUILD +verify-bazel
    BUILD +verify-boilerplate
    BUILD +verify-cli-conventions
    BUILD +verify-codegen
    BUILD +verify-conformance-requirements
    BUILD +verify-conformance-yaml
    BUILD +verify-description
    BUILD +verify-e2e-test-ownership
    BUILD +verify-external-dependencies-version
    BUILD +verify-flags-underscore
    BUILD +verify-generated-device-plugin
    BUILD +verify-generated-docs
    BUILD +verify-generated-files-remake
    BUILD +verify-generated-files
    BUILD +verify-generated-kms
    BUILD +verify-generated-kubelet-plugin-registration
    BUILD +verify-generated-pod-resources
    BUILD +verify-generated-protobuf
    BUILD +verify-generated-runtime
    BUILD +verify-generated-stable-metrics
    BUILD +verify-generated-swagger-docs
    BUILD +verify-gofmt
    BUILD +verify-golangci-lint
    BUILD +verify-govet
    # skip verify-govet-levee while KEP-1933 is being implemented
    # BUILD +verify-govet-levee
    BUILD +verify-import-aliases
    BUILD +verify-import-boss
    BUILD +verify-imports
    BUILD +verify-internal-modules
    # skip verify-linkcheck since it's very network intensive
    # BUILD +verify-linkcheck
    BUILD +verify-mocks
    BUILD +verify-netparse-cve
    BUILD +verify-no-vendor-cycles
    BUILD +verify-openapi-spec
    BUILD +verify-pkg-names
    BUILD +verify-prerelease-lifecycle-tags
    BUILD +verify-prometheus-imports
    BUILD +verify-publishing-bot
    BUILD +verify-readonly-packages
    BUILD +verify-shellcheck
    BUILD +verify-spelling
    BUILD +verify-staging-meta-files
    BUILD +verify-staticcheck
    BUILD +verify-structured-logging
    BUILD +verify-test-code
    BUILD +verify-test-featuregates
    BUILD +verify-test-images
    BUILD +verify-typecheck
    BUILD +verify-vendor-licences
    BUILD +verify-vendor


# verify-legacy is the same as calling "make verify" where all verify checks
# run sequentially. Use the +verify target to run all verify checks in parallel.
verify-legacy:
    FROM +src
    WITH DOCKER
        RUN hack/make-rules/verify.sh
    END


# quick-verify is like calling "QUICK=true make verify" but each check
# is run in parallel as isolated earthly targets
quick-verify:
    BUILD +verify-api-groups
    BUILD +verify-boilerplate
    BUILD +verify-external-dependencies-version
    BUILD +verify-vendor-licences
    BUILD +verify-gofmt
    BUILD +verify-imports
    BUILD +verify-pkg-names
    BUILD +verify-readonly-packages
    BUILD +verify-spelling
    BUILD +verify-staging-meta-files
    BUILD +verify-test-featuregates
    BUILD +verify-test-images


# quick-verify-legacy is the same as running "QUICK=true SILENT=false make verify" where
# verify checks that only take a few seconds are run sequentially.
quick-verify-legacy:
    FROM +src
    RUN QUICK=true SILENT=false hack/make-rules/verify.sh


verify-api-groups:
    FROM +src
    RUN hack/verify-api-groups.sh


verify-bazel:
    FROM +src
    RUN hack/verify-bazel.sh


verify-boilerplate:
    FROM +src
    RUN hack/verify-boilerplate.sh


verify-cli-conventions:
    FROM +src
    RUN hack/verify-cli-conventions.sh


verify-codegen:
    FROM +src
    RUN make generated_files
    RUN hack/verify-codegen.sh


verify-conformance-requirements:
    FROM +src
    RUN hack/verify-conformance-requirements.sh


verify-conformance-yaml:
    FROM +src
    RUN hack/verify-conformance-yaml.sh


verify-description:
    FROM +src
    RUN hack/verify-description.sh


verify-e2e-test-ownership:
    FROM +src
    RUN hack/verify-e2e-test-ownership.sh


verify-external-dependencies-version:
    FROM +src
    RUN hack/verify-external-dependencies-version.sh


verify-flags-underscore:
    FROM +src
    RUN hack/verify-flags-underscore.py


verify-generated-device-plugin:
    FROM +src
    RUN hack/verify-generated-device-plugin.sh


verify-generated-docs:
    FROM +src
    RUN hack/verify-generated-docs.sh


verify-generated-files-remake:
    FROM +src
    RUN hack/verify-generated-files-remake.sh


verify-generated-files:
    FROM +src
    RUN hack/verify-generated-files.sh


verify-generated-kms:
    FROM +src
    RUN hack/verify-generated-kms.sh


verify-generated-kubelet-plugin-registration:
    FROM +src
    RUN hack/verify-generated-kubelet-plugin-registration.sh


verify-generated-pod-resources:
    FROM +src
    RUN hack/verify-generated-pod-resources.sh


verify-generated-protobuf:
    FROM +src
    RUN hack/verify-generated-protobuf.sh


verify-generated-runtime:
    FROM +src
    RUN hack/verify-generated-runtime.sh


verify-generated-stable-metrics:
    FROM +src
    RUN hack/verify-generated-stable-metrics.sh


verify-generated-swagger-docs:
    FROM +src
    RUN hack/verify-generated-swagger-docs.sh


verify-gofmt:
    FROM +src
    RUN hack/verify-gofmt.sh


verify-golangci-lint:
    FROM +src
    RUN hack/verify-golangci-lint.sh


verify-govet-levee:
    FROM +src
    RUN hack/verify-govet-levee.sh


verify-govet:
    FROM +src
    RUN hack/verify-govet.sh


verify-import-aliases:
    FROM +src
    RUN hack/verify-import-aliases.sh


verify-import-boss:
    FROM +src
    RUN hack/verify-import-boss.sh


verify-imports:
    FROM +src
    RUN hack/verify-imports.sh


verify-internal-modules:
    FROM +src
    RUN hack/verify-internal-modules.sh


verify-linkcheck:
    FROM +src
    RUN hack/verify-linkcheck.sh


verify-mocks:
    FROM +src
    RUN hack/verify-mocks.sh


verify-netparse-cve:
    FROM +src
    RUN hack/verify-netparse-cve.sh


verify-no-vendor-cycles:
    FROM +src
    RUN hack/verify-no-vendor-cycles.sh


verify-openapi-spec:
    FROM +src
    RUN hack/verify-openapi-spec.sh


verify-pkg-names:
    FROM +src
    RUN hack/verify-pkg-names.sh


verify-prerelease-lifecycle-tags:
    FROM +src
    RUN hack/verify-prerelease-lifecycle-tags.sh


verify-prometheus-imports:
    FROM +src
    RUN hack/verify-prometheus-imports.sh


verify-publishing-bot:
    FROM +src
    RUN hack/verify-publishing-bot.py


verify-readonly-packages:
    FROM +src
    RUN hack/verify-readonly-packages.sh


verify-shellcheck:
    FROM +src
    RUN hack/verify-shellcheck.sh


verify-spelling:
    FROM +src
    RUN hack/verify-spelling.sh


verify-staging-meta-files:
    FROM +src
    RUN hack/verify-staging-meta-files.sh


verify-staticcheck:
    FROM +src
    RUN hack/verify-staticcheck.sh


verify-structured-logging:
    FROM +src
    RUN hack/verify-structured-logging.sh


verify-test-code:
    FROM +src
    RUN hack/verify-test-code.sh


verify-test-featuregates:
    FROM +src
    RUN hack/verify-test-featuregates.sh


verify-test-images:
    FROM +src
    RUN hack/verify-test-images.sh


verify-typecheck:
    FROM +src
    RUN hack/verify-typecheck-dockerless.sh
    RUN hack/verify-typecheck-providerless.sh
    RUN hack/verify-typecheck.sh


verify-vendor-licences:
    FROM +src
    RUN hack/verify-vendor-licenses.sh


verify-vendor:
    FROM +src
    RUN hack/verify-vendor.sh


vet:
    FROM +src
    RUN CALLED_FROM_MAIN_MAKEFILE=1 hack/make-rules/vet.sh $WHAT


# Build targets are actually not run in parallel when using LOCALLY so this should be safe.
update:
    LOCALLY
    RUN ./hack/install-etcd.sh
    BUILD +update-generated-protobuf
    BUILD +update-codegen
    BUILD +update-generated-runtime
    BUILD +update-generated-device-plugin
    BUILD +update-generated-api-compatibility-data
    BUILD +update-generated-docs
    BUILD +update-generated-swagger-docs
    BUILD +update-openapi-spec
    BUILD +update-bazel
    BUILD +update-gofmt


# update-legacy is the same as running "make update" where each update
# script is run sequentially. Use the +update target to run updates in parallel.
update-legacy:
    LOCALLY
    RUN ./hack/install-etcd.sh
    RUN CALLED_FROM_MAIN_MAKEFILE=1 hack/make-rules/update.sh


update-generated-protobuf:
    LOCALLY
    RUN ./hack/update-generated-protobuf.sh


update-codegen:
    LOCALLY
    RUN ./hack/update-codegen.sh


update-generated-runtime:
    LOCALLY
    RUN ./hack/update-generated-runtime.sh


update-generated-device-plugin:
    LOCALLY
    RUN ./hack/update-generated-device-plugin.sh


update-generated-api-compatibility-data:
    LOCALLY
    RUN ./hack/update-generated-api-compatibility-data.sh


update-generated-docs:
    LOCALLY
    RUN ./hack/update-generated-docs.sh


update-generated-swagger-docs:
    LOCALLY
    RUN ./hack/update-generated-swagger-docs.sh


update-openapi-spec:
    LOCALLY
    RUN ./hack/update-openapi-spec.sh


update-bazel:
    LOCALLY
    RUN ./hack/update-bazel.sh


update-gofmt:
    LOCALLY
    RUN ./hack/update-gofmt.sh


test-all:
   BUILD +test
   BUILD +test-integration
   BUILD +test-cmd


test:
    FROM +src
    RUN ./hack/make-rules/test.sh $WHAT $TESTS


test-integration:
    FROM +src
    RUN ./hack/make-rules/test-integration.sh $WHAT


test-cmd:
    FROM +src
    RUN ./hack/make-rules/test-cmd.sh


release:
    FROM +src
    RUN ./hack/install-etcd.sh
    WITH DOCKER
        RUN ./build/release.sh
    END
    SAVE ARTIFACT _output AS LOCAL _output


release-in-a-container:
    FROM +src
    RUN ./hack/install-etcd.sh
    WITH DOCKER
        RUN ./build/release-in-a-container.sh
    END
    SAVE ARTIFACT _output AS LOCAL _output


release-images:
    FROM +src
    RUN ./hack/install-etcd.sh
    WITH DOCKER
        RUN ./build/release-images.sh
    END
    SAVE ARTIFACT _output AS LOCAL _output


quick-release:
    FROM +src
    ENV KUBE_RELEASE_RUN_TESTS=n
    ENV KUBE_FASTBUILD=true
    WITH DOCKER
        RUN ./build/release.sh
    END
    SAVE ARTIFACT _output AS LOCAL _output


quick-release-images:
    FROM +src
    ENV KUBE_FASTBUILD=true
    WITH DOCKER
        RUN ./build/release-images.sh
    END
    SAVE ARTIFACT _output AS LOCAL _output


package-tarballs:
    FROM +src
    ENV KUBE_BUILD_CONFORMANCE=y
    WITH DOCKER
        RUN ./build/package-tarballs.sh
    END
    SAVE ARTIFACT _output AS LOCAL _output
