FROM ubuntu:20.04

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # Update & upgrade
    #
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get update \
    && apt-get upgrade \
    #
    # Add sudo support
    #
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    #
    # Install required packages (and zsh)
    #
    && apt-get install -y git \
                          curl \
                          wget \
                          bash \
                          zsh \
                          zsh-syntax-highlighting \
                          zsh-autosuggestions \
                          software-properties-common \
                          apt-transport-https \
                          jq \
                          unzip \
                          gnupg \
                          lsb-release \
    #
    # Install az cli
    #
    && set -v \
    && curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | tee /etc/apt/trusted.gpg.d/microsoft.asc.gpg >/dev/null \
    && CLI_REPO=$(lsb_release -cs) \
    && echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ ${CLI_REPO} main" | tee /etc/apt/sources.list.d/azure-cli.list \
    && apt-get update \
    && set +v \
    && apt-get install -y azure-cli \
    #
    # Install terraform & tflint
    #
    && TF_VER=$(curl -s https://checkpoint-api.hashicorp.com/v1/check/terraform | jq -r -M '.current_version') \
    && curl "https://releases.hashicorp.com/terraform/${TF_VER}/terraform_${TF_VER}_linux_amd64.zip" --output ~/terraform_${TF_VER}_linux_amd64.zip \
    && TFLINT_URL="https://github.com/terraform-linters/tflint/releases/latest/download/tflint_linux_amd64.zip" \
    && curl -L $TFLINT_URL --output ~/tflint_linux_amd64.zip \
    && unzip ~/terraform_${TF_VER}_linux_amd64.zip -d /usr/local/bin \
    && unzip ~/tflint_linux_amd64.zip -d /usr/local/bin \
    && rm ~/tflint_linux_amd64.zip ~/terraform_${TF_VER}_linux_amd64.zip

USER $USERNAME

RUN echo "alias t='noglob terraform'" | tee -a ~/.zshrc \
    && echo "alias terraform='noglob terraform'" | tee -a ~/.zshrc
