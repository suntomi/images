FROM hashicorp/terraform:1.1.3

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && rm -rf awscliv2.zip aws

RUN apk add docker 
