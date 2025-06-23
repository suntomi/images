FROM amazonlinux:2 AS installer
ENV TERRAFORM_VERSION=1.9.7
ARG TARGETARCH
ENV TARGETARCH=${TARGETARCH}
RUN yum update -y \
  && yum install -y unzip \
  && if [ "${TARGETARCH}" = "arm64" ]; then export ARCH="aarch64"; else export ARCH="x86_64"; fi \
  && curl -L https://awscli.amazonaws.com/awscli-exe-linux-${ARCH}.zip -o awscli-exe-linux-${ARCH}.zip \
  && unzip awscli-exe-linux-${ARCH}.zip \
  # The --bin-dir is specified so that we can copy the
  # entire bin directory from the installer stage into
  # into /usr/local/bin of the final stage without
  # accidentally copying over any other executables that
  # may be present in /usr/local/bin of the installer stage.
  && ./aws/install --bin-dir /aws-cli-bin/ \
  && curl -L "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_${TARGETARCH}.zip" -o terraform.zip \
  && unzip terraform.zip

FROM amazonlinux:2
ARG TARGETARCH
ENV TARGETARCH=${TARGETARCH}
COPY --from=installer /usr/local/aws-cli/ /usr/local/aws-cli/
COPY --from=installer /aws-cli-bin/ /usr/local/bin/
COPY --from=installer terraform /usr/bin/
RUN yum install -y jq git tar \
  && yum clean all \
  && amazon-linux-extras install docker
ENV YQ_VERSION=v4.44.3
ENV YQ_BINARY=yq_linux_${TARGETARCH}
RUN curl -v -L -o /tmp/yq https://github.com/mikefarah/yq/releases/download/${YQ_VERSION}/${YQ_BINARY} \
  && mv /tmp/yq /usr/bin/yq && chmod +x /usr/bin/yq
RUN curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` \
  -o /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose
RUN curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/${TARGETARCH}/kubectl" && \
    chmod +x ./kubectl && mv ./kubectl /usr/local/bin/kubectl
RUN cd /usr/local/bin && curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash

ENTRYPOINT ["/bin/sh"]
