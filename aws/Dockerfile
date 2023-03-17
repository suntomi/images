FROM amazonlinux:2 as installer
ENV TERRAFORM_VERSION 1.4.2
RUN yum update -y \
  && yum install -y unzip \
  && curl https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip -o awscli-exe-linux-x86_64.zip \
  && unzip awscli-exe-linux-x86_64.zip \
  # The --bin-dir is specified so that we can copy the
  # entire bin directory from the installer stage into
  # into /usr/local/bin of the final stage without
  # accidentally copying over any other executables that
  # may be present in /usr/local/bin of the installer stage.
  && ./aws/install --bin-dir /aws-cli-bin/ \
  && curl "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip" -o terraform.zip \
  && unzip terraform.zip

FROM amazonlinux:2
COPY --from=installer /usr/local/aws-cli/ /usr/local/aws-cli/
COPY --from=installer /aws-cli-bin/ /usr/local/bin/
COPY --from=installer terraform /usr/bin/
RUN yum install -y jq git tar \
  && yum clean all \
  && amazon-linux-extras install docker
ENV YQ_VERSION v4.16.2
ENV YQ_BINARY yq_linux_amd64
RUN curl -v -L -o /tmp/yq https://github.com/mikefarah/yq/releases/download/${YQ_VERSION}/${YQ_BINARY} \
  && mv /tmp/yq /usr/bin/yq && chmod +x /usr/bin/yq
RUN curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` \
  -o /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose
RUN curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl" && \
    chmod +x ./kubectl && mv ./kubectl /usr/local/bin/kubectl
RUN cd /usr/local/bin && curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash

ENTRYPOINT ["/bin/sh"]
