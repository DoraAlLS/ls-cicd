# Use Jenkins SSH agent with JDK 17 as the base image
FROM jenkins/ssh-agent:alpine-jdk17

# Set environment variables for Jenkins
ENV JENKINS_AGENT_HOME /home/jenkins
ENV PATH="${JENKINS_AGENT_HOME}/.local/bin:${PATH}"

# Switch to root to perform setup tasks
USER root

# Ensure the Jenkins home directory and .local/bin exist and have the correct permissions
RUN mkdir -p ${JENKINS_AGENT_HOME}/.local/bin && \
    chown -R jenkins:jenkins ${JENKINS_AGENT_HOME}

# Display current user and available users (for debugging)
RUN echo "Current user and user ID info:" && id && \
    echo "Available users:" && cat /etc/passwd

# Install Python 3 and pip
RUN apk update && \
    apk add --no-cache python3 py3-pip && \
    ln -s /usr/bin/python3 ${JENKINS_AGENT_HOME}/.local/bin/python && \
    ln -s /usr/bin/pip3 ${JENKINS_AGENT_HOME}/.local/bin/pip && \
    chown -R jenkins:jenkins ${JENKINS_AGENT_HOME}/.local

# Verify Python installation
RUN python3 --version && pip3 --version

# Set up a virtual environment for the Jenkins user and install jinja2 there
RUN python3 -m venv ${JENKINS_AGENT_HOME}/venv && \
    ${JENKINS_AGENT_HOME}/venv/bin/pip install jinja2

# add the venv path to allow jenkins to prioritize packages in the venv
ENV PATH="${JENKINS_AGENT_HOME}/venv/bin:${PATH}"

# Switch back to Jenkins user for the remainder of the build process
USER jenkins

