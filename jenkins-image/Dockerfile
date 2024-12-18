FROM jenkins/jenkins:2.479.2-jdk17
USER root
ENV TZ=Asia/Jerusalem
RUN apt-get update && apt-get install -y lsb-release
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
  https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce-cli
RUN apt-get update && apt-get install -y tzdata vim-tiny && \
    ln -sf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata
ENV JAVA_OPTS="-Dorg.apache.commons.jelly.tags.fmt.timeZone=$TZ"

# ansible installation
ENV PYTHONUNBUFFERED=1 \
    VENV_DIR="/ansible/venv" \
    PATH="/ansible/venv/bin:$PATH"

# Set up environment variables
ENV VENV_DIR="/opt/ansible/venv" \
    PATH="/opt/ansible/venv/bin:$PATH" \
    PYTHONUNBUFFERED=1

# Install dependencies and Python
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 \
    python3-venv \
    python3-pip \
    libssl-dev \
    libffi-dev \
    build-essential \
    sshpass \
    git \
    && rm -rf /var/lib/apt/lists/*

# Install Ansible into a virtual environment
RUN mkdir -p /opt/ansible \
    && python3 -m venv ${VENV_DIR} \
    && ${VENV_DIR}/bin/pip install --upgrade pip setuptools wheel \
    && ${VENV_DIR}/bin/pip install ansible \
    && ${VENV_DIR}/bin/ansible --version

RUN mkdir -p /playbooks-vol

# Add a health check to verify Ansible
RUN which ansible && ansible --version


USER jenkins
