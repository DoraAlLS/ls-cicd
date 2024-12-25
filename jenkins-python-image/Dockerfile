FROM almalinux:minimal
<<<<<<< HEAD:jenkins-python-image/Dockerfile

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
=======

# Install required packages
RUN microdnf install -y \
        git \
        openssh-server \
        java-11-openjdk-headless \
        python3 \
        python3-pip \
        --nodocs && \
    microdnf clean all && \
    rm -rf /usr/share/man /usr/share/doc /usr/share/locale /var/cache/* /tmp/*

# Enable and prepare the SSH server
RUN mkdir -p /var/run/sshd && \
>>>>>>> 065ff0a (push changes from jenkisn server):jenkins-python-agent/Dockerfile
    useradd -m -s /bin/bash jenkins && \
    echo "jenkins:jenkins" | chpasswd

# Copy authorized keys
<<<<<<< HEAD:jenkins-python-image/Dockerfile
COPY --chown=jenkins ./authorized_keys /home/jenkins/.ssh/authorized_keys
COPY ./sshd_config /etc/ssh/
=======
COPY --chown=jenkins authorized_keys /home/jenkins/.ssh/authorized_keys
>>>>>>> 065ff0a (push changes from jenkisn server):jenkins-python-agent/Dockerfile
RUN chown -R jenkins:jenkins /home/jenkins/.ssh/ && \
    chmod 700 /home/jenkins/.ssh/ && \
    chmod 600 /home/jenkins/.ssh/authorized_keys

<<<<<<< HEAD:jenkins-python-image/Dockerfile
RUN python3 -m pip install --upgrade pip && \
    pip install --no-cache-dir jinja2 semver
=======
# Install Python dependencies
RUN pip install --no-cache-dir jinja2 semver
>>>>>>> 065ff0a (push changes from jenkisn server):jenkins-python-agent/Dockerfile

# Expose SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
