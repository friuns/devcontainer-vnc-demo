FROM mcr.microsoft.com/devcontainers/base:ubuntu

# Install desktop environment and VNC server
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        xfce4 \
        xfce4-goodies \
        tightvncserver \
        novnc \
        websockify \
        xfonts-base \
        xfonts-75dpi \
        xfonts-100dpi \
        xfonts-scalable \
        fonts-liberation \
        fonts-dejavu \
        firefox \
        xterm \
        && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/*

# Set up VNC
RUN mkdir -p /home/vscode/.vnc \
    && echo "vscode" | vncpasswd -f > /home/vscode/.vnc/passwd \
    && chmod 600 /home/vscode/.vnc/passwd \
    && chown -R vscode:vscode /home/vscode/.vnc

# Set up noVNC
RUN mkdir -p /opt/novnc \
    && cd /opt/novnc \
    && wget -qO- https://github.com/novnc/noVNC/archive/v1.4.0.tar.gz | tar xz --strip 1 \
    && ln -s /opt/novnc/vnc.html /opt/novnc/index.html

# Create startup script
RUN echo '#!/bin/bash\n\
export USER=vscode\n\
export HOME=/home/vscode\n\
cd $HOME\n\
\n\
# Start VNC server\n\
vncserver -geometry 1280x720 :1\n\
\n\
# Start noVNC\n\
/opt/novnc/utils/novnc_proxy --vnc localhost:5901 --listen 6080 &\n\
\n\
echo "VNC server running on port 5901"\n\
echo "noVNC web interface available on port 6080"\n\
echo "VNC password: vscode"\n\
\n\
# Keep container running\n\
tail -f /dev/null' > /usr/local/bin/start-vnc.sh \
    && chmod +x /usr/local/bin/start-vnc.sh

USER vscode
