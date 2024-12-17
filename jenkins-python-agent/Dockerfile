FROM centos:7

# Update the repository and install required packages
RUN yum -y update && \
    yum -y install epel-release && \
    yum -y install git openssh-server java-11-openjdk-devel python3 pip && \
    yum -y autoremove && \
    yum clean all && \
# Enable and prepare the SSH server
    mkdir -p /var/run/sshd && \
# Add user jenkins to the image
    useradd -m -s /bin/bash jenkins && \
# Set password for the jenkins user
    echo "jenkins:jenkins" | chpasswd

# Copy authorized keys
COPY --chown=jenkins ./authorized_keys /home/jenkins/.ssh/authorized_keys
RUN chown -R jenkins:jenkins /home/jenkins/.ssh/ && \
    chmod 700 /home/jenkins/.ssh/ && \
    chmod 600 /home/jenkins/.ssh/authorized_keys

RUN pip install jinja2

# Standard SSH port
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
