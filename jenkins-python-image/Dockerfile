FROM almalinux:minimal

# Update the repository and install required packages
RUN microdnf install -y \
	git \
	openssh-server \
	java-17-openjdk-headless \
	python3 \
	python3-pip \
	--nodocs && \
    microdnf clean all && \
    rm -rf /usr/share/man /usr/share/doc /usr/share/locale /var/cache/* /tmp/*
# Enable and prepare the SSH server

RUN    mkdir -p /var/run/sshd && \
# Generate SSH Host Keys
    ssh-keygen -A && \
# Add user jenkins to the image
    useradd -m -s /bin/bash jenkins && \
    echo "jenkins:jenkins" | chpasswd

# Copy authorized keys
COPY --chown=jenkins ./authorized_keys /home/jenkins/.ssh/authorized_keys
COPY ./sshd_config /etc/ssh/
RUN chown -R jenkins:jenkins /home/jenkins/.ssh/ && \
    chmod 700 /home/jenkins/.ssh/ && \
    chmod 600 /home/jenkins/.ssh/authorized_keys

RUN python3 -m pip install --upgrade pip && \
    pip install --no-cache-dir jinja2 semver

# Expose SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
