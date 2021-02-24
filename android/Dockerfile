FROM ubuntu:18.04

ENV DEBIAN_FRONTEND noninteractive

ENV ANDROID_HOME      /opt/android-sdk-linux
ENV ANDROID_SDK_HOME  ${ANDROID_HOME}
ENV ANDROID_SDK_ROOT  ${ANDROID_HOME}
ENV ANDROID_SDK       ${ANDROID_HOME}

ENV PATH "${PATH}:${ANDROID_HOME}/cmdline-tools/latest/bin"
ENV PATH "${PATH}:${ANDROID_HOME}/cmdline-tools/tools/bin"
ENV PATH "${PATH}:${ANDROID_HOME}/tools/bin"
ENV PATH "${PATH}:${ANDROID_HOME}/build-tools/30.0.2"
ENV PATH "${PATH}:${ANDROID_HOME}/platform-tools"
ENV PATH "${PATH}:${ANDROID_HOME}/emulator"
ENV PATH "${PATH}:${ANDROID_HOME}/bin"

RUN dpkg --add-architecture i386 && \
    apt-get update -yqq && \
    apt-get install -y curl expect git libc6:i386 libgcc1:i386 libncurses5:i386 libstdc++6:i386 zlib1g:i386 openjdk-8-jdk wget unzip vim ruby build-essential ruby-dev locales && \
    apt-get clean

RUN groupadd android && useradd -d /opt/android-sdk-linux -g android android

COPY tools /opt/tools
COPY licenses /opt/licenses

WORKDIR /opt/android-sdk-linux

RUN /opt/tools/entrypoint.sh built-in

RUN /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "cmdline-tools;latest"
RUN /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "build-tools;30.0.2"
RUN /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "platform-tools"
RUN /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "platforms;android-29"
RUN /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "system-images;android-29;google_apis;x86_64"

RUN mkdir -p /usr/src/app

# Install bundler
RUN gem update --system '2.7.9'; \
    gem install bundle; \
    gem install bundler

# Preinstall some packages
COPY gem/ /usr/src/gem
RUN cd /usr/src/gem; \
    bundle install --jobs=4; \
    rm -rf /usr/src/gem

# Install node 14.15.5 + yarn
RUN cd /tmp; \
    wget --quiet https://nodejs.org/dist/v14.15.5/node-v14.15.5-linux-x64.tar.xz; \
    tar xf node-v14.15.5-linux-x64.tar.xz; \
    cp -r node-v14.15.5-linux-x64/* /usr/; \
    rm -rf node-v14.15.5-linux-x64*; \
    npm -g install yarn

# Prepare gradle
COPY gradle /usr/src/gradle/
RUN cd /usr/src/gradle; \
    ./gradlew --no-daemon; \
    rm -rf /usr/src/gradle

# Install gradle and launch gradle daemon
#ENV GRADLE_VERSION=6.2
#RUN cd /tmp; \
#    wget https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip; \
#    mkdir -p /opt/gradle; \
#    unzip -d /opt/gradle/ gradle-${GRADLE_VERSION}-bin.zip
#ENV PATH "${PATH}:/opt/gradle/gradle-${GRADLE_VERSION}/bin"

# Set proper locales
RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen; \
    locale-gen
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8

