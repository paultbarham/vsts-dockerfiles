# Build a Team Services coreCLR Ubunut xplat build machine

FROM javaalm/vsts-ubuild-full 

MAINTAINER Paul T. Barham <pabarham@microsoft.com>

LABEL version="RC2"
LABEL description="An Ubuntu VM with cross-platform development tools, Team Services coreCLR build agent and Java ALM Team Libraries"

USER root

# Install needed VS Code Node modules
RUN npm install -g vscode
RUN npm install -g vsce

# Install Oracle Java in silent mode
RUN echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections
RUN echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections
RUN add-apt-repository -y ppa:webupd8team/java
RUN apt-get -y update
RUN apt-get install -y oracle-java6-installer
RUN apt-get install -y oracle-java7-installer
RUN apt-get install -y oracle-java8-installer
RUN apt-get -y update

# Download Team needed libraries
WORKDIR /home/xplatalm
ADD https://xplatalm.blob.core.windows.net/buildlibs/git4idea.zip downloads/
ADD https://xplatalm.blob.core.windows.net/buildlibs/eclipse.egit.repository-2.1.0.201209190230-r.zip downloads/
ADD https://xplatalm.blob.core.windows.net/buildlibs/eclipse-SDK-3.5.2-linux-gtk-x86_64.tar.gz downloads/
ADD https://xplatalm.blob.core.windows.net/buildlibs/ideaIC-14.1.4.zip downloads/
# ADD https://xplatalm.blob.core.windows.net/buildlibs/ideaIC-15.0.6.tar.gz downloads/
# ADD https://xplatalm.blob.core.windows.net/buildlibs/ideaIC-2016.1.2b.tar.gz downloads/
RUN chmod 0666 -R downloads/*

USER xplatalm

# Set reqiored environment variables
ENV JAVA_HOME_6_X64 /usr/lib/jvm/java-6-oracle
ENV JAVA_HOME_7_X64 /usr/lib/jvm/java-7-oracle
ENV JAVA_HOME_8_X64 /usr/lib/jvm/java-8-oracle
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle
ENV JAVA_TOOL_OPTIONS -Dfile.encoding=UTF8

RUN echo "export JAVA_HOME_6_X64=/usr/lib/jvm/java-6-oracle" >> /home/xplatalm/.bashrc && \
    echo "export JAVA_HOME_7_X64=/usr/lib/jvm/java-7-oracle" >> /home/xplatalm/.bashrc && \
    echo "export JAVA_HOME_8_X64=/usr/lib/jvm/java-8-oracle" >> /home/xplatalm/.bashrc && \
    echo "export JAVA_HOME=/usr/lib/jvm/java-8-oracle" >> /home/xplatalm/.bashrc && \
    echo "export JAVA_TOOL_OPTIONS=-Dfile.encoding=UTF8" >> /home/xplatalm/.bashrc

# Install libraries for Eclipse, IntelliJ
WORKDIR /home/xplatalm/lib
RUN tar xzvf /home/xplatalm/downloads/eclipse-SDK-3.5.2-linux-gtk-x86_64.tar.gz
RUN mv eclipse eclipse-SDK-3.5.2
WORKDIR eclipse-SDK-3.5.2
RUN unzip /home/xplatalm/downloads/eclipse.egit.repository-2.1.0.201209190230-r.zip
WORKDIR /home/xplatalm/lib
RUN unzip /home/xplatalm/downloads/ideaIC-14.1.4.zip
# RUN tar xzvf /home/xplatalm/downloads/ideaIC-15.0.6.tar.gz
# RUN tar xzvf /home/xplatalm/downloads/ideaIC-2016.1.2b.tar.gz
RUN ln -s idea-14.1.4 idea
RUN unzip /home/xplatalm/downloads/git4idea.zip
RUN mkdir idea-14.1.4/plugins/
RUN mkdir idea-14.1.4/plugins/git4idea/
RUN mkdir idea-14.1.4/plugins/git4idea/lib
RUN mv git4idea/git4idea.jar idea-14.1.4/plugins/git4idea/lib/

WORKDIR /home/xplatalm

# Run Gradle once to make sure .gradle directory is created
# RUN /usr/bin/gradle

# Configure Team gradle properties
RUN echo "ideaSdk=/home/xplatalm/lib/idea" > /home/xplatalm/.gradle/gradle.properties
RUN echo "git4idea=/home/xplatalm/lib/idea/plugins/git4idea/lib" >> /home/xplatalm/.gradle/gradle.properties

USER root

# Best practice to clean up some files to make Docker image smaller
RUN rm -rf /var/lib/apt/lists/*
RUN rm -rf /home/xplatalm/.gradle/caches/*
RUN rm -rf /home/xplatalm/downloads

# Leave the container running as xplatalm user in the VSTS Agent directory
USER xplatalm
WORKDIR /home/xplatalm/vsts-agent/
RUN /bin/bash -c "source /home/xplatalm/.bashrc"

