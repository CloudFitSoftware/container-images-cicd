FROM ubuntu:24.04
ENV TARGETARCH="linux-x64"

# Install dependencies
RUN apt-get update && apt-get upgrade -y && apt-get install -y --no-install-recommends \
    curl \
    git \
    unzip \
    jq \
    libicu74 \
    apt-transport-https \
    gnupg \
    lsb-release \
    certbot

# Install Azure CLI
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

# Install Docker CLI
RUN apt-get update && \
    apt-get install -y ca-certificates curl && \
    install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc && \
    chmod a+r /etc/apt/keyrings/docker.asc && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu noble stable" | \
    tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    apt-get update && \
    apt-get install -y docker-ce-cli && \
    rm -rf /var/lib/apt/lists/*

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" \
    && install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Install Helm 
RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 \
    && chmod +x get_helm.sh \
    && ./get_helm.sh \
    && rm get_helm.sh

# Install Azure Pipelines agent (use latest version or pin to specific)
RUN mkdir -p /azp \
    && curl -fsSL -o /azp/vsts-agent-linux-x64-4.263.0.tar.gz https://download.agent.dev.azure.com/agent/4.263.0/vsts-agent-linux-x64-4.263.0.tar.gz \
    && tar zxvf /azp/vsts-agent-linux-x64-*.tar.gz -C /azp \
    && rm /azp/vsts-agent-linux-x64-*.tar.gz

# Set up the Azure DevOps agent
WORKDIR /azp/
COPY ./start.sh ./
RUN chmod +x ./start.sh

# Create agent user and set up home directory
RUN useradd -m -d /home/agent agent
RUN chown -R agent:agent /azp /home/agent

USER agent

ENTRYPOINT [ "./start.sh" ]