FROM almalinux:minimal

# ansible installation
ENV PYTHONUNBUFFERED=1 \
    VENV_DIR="/ansible/venv" \
    PATH="/ansible/venv/bin:$PATH"

# Set up environment variables
ENV VENV_DIR="/opt/ansible/venv" \
    PATH="/opt/ansible/venv/bin:$PATH" \
    PYTHONUNBUFFERED=1

# Update the repository and install required packages
RUN microdnf install -y \
	git \
	openssh-server \
	java-17-openjdk-headless \
	python3 \
	python3-pip \
    python3-virtualenv \
    libssl-dev \
    libffi-dev \
    build-essential \
    sshpass \
	--nodocs && \
    microdnf clean all && \
    rm -rf /usr/share/man /usr/share/doc /usr/share/locale /var/cache/* /tmp/*
# Enable and prepare the SSH server

RUN    mkdir -p /var/run/sshd && \
# Generate SSH Host Keys
    ssh-keygen -A && \
# Add user jenkins to the image
    useradd -m -s /bin/bash jenkins && \
# Set password for the jenkins user
    echo "jenkins:jenkins" | chpasswd

# Copy authorized keys
COPY --chown=jenkins ./authorized_keys /home/jenkins/.ssh/authorized_keys
COPY ./sshd_config /etc/ssh/
RUN chown -R jenkins:jenkins /home/jenkins/.ssh/ && \
    chmod 700 /home/jenkins/.ssh/ && \
    chmod 600 /home/jenkins/.ssh/authorized_keys

# Install Ansible into a virtual environment
RUN mkdir -p /opt/ansible \
    && python3 -m venv ${VENV_DIR} \
    && ${VENV_DIR}/bin/pip install --upgrade pip setuptools wheel \
    && ${VENV_DIR}/bin/pip install ansible \
    && ${VENV_DIR}/bin/ansible --version

RUN mkdir -p /playbooks-vol

# Add a health check to verify Ansible
RUN which ansible && ansible --version

# Standard SSH port
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

