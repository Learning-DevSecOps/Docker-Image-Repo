# Use Ubuntu as base image for better compatibility with GitLab CI
FROM ubuntu:22.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV MAVEN_HOME=/opt/maven
ENV SONAR_SCANNER_HOME=/opt/sonar-scanner
ENV PATH="${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${SONAR_SCANNER_HOME}/bin:${PATH}"

# Define versions
ENV MAVEN_VERSION=3.9.6
ENV SONAR_SCANNER_VERSION=5.0.1.3006

# Install system dependencies and tools in a single layer
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        openjdk-17-jdk \
        wget \
        curl \
        telnet \
        zip \
        unzip \
        net-tools \
        ca-certificates \
        git \
        openssh-client && \
    # Clean up apt cache to reduce image size
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install Maven
RUN wget -q https://archive.apache.org/dist/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz && \
    tar -xzf apache-maven-${MAVEN_VERSION}-bin.tar.gz -C /opt && \
    mv /opt/apache-maven-${MAVEN_VERSION} ${MAVEN_HOME} && \
    rm apache-maven-${MAVEN_VERSION}-bin.tar.gz

# Install SonarQube Scanner
RUN wget -q https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip && \
    unzip -q sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip -d /opt && \
    mv /opt/sonar-scanner-${SONAR_SCANNER_VERSION}-linux ${SONAR_SCANNER_HOME} && \
    rm sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip

# Create maven cache directory for GitLab CI optimization
RUN mkdir -p /root/.m2

# Create a non-root user for better security (optional, comment out if not needed)
RUN useradd -m -s /bin/bash gitlab-ci && \
    mkdir -p /home/gitlab-ci/.m2 && \
    chown -R gitlab-ci:gitlab-ci /home/gitlab-ci

# Set working directory
WORKDIR /builds

# Verify installations
RUN java -version && \
    mvn -version && \
    sonar-scanner -v && \
    echo "All tools installed successfully"

# Default command
CMD ["/bin/bash"]