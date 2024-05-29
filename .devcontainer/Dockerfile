FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

RUN wget -qO - 'https://proget.makedeb.org/debian-feeds/prebuilt-mpr.pub' | gpg --dearmor | sudo tee /usr/share/keyrings/prebuilt-mpr-archive-keyring.gpg 1> /dev/null && \
    echo "deb [arch=all,$(dpkg --print-architecture) signed-by=/usr/share/keyrings/prebuilt-mpr-archive-keyring.gpg] https://proget.makedeb.org prebuilt-mpr $(lsb_release -cs)" | sudo tee /etc/apt/sources.list.d/prebuilt-mpr.list && \
    sudo apt-get update && \
    sudo apt-get -y install just && \
    rm -rf /var/lib/apt/lists/*

RUN mkdir /home/vscode/.ssh
RUN chmod -R 777 /home/vscode
RUN usermod -aG sudo vscode

USER vscode

ENV PATH="/home/vscode/.local/bin:$PATH"
