FROM openjdk:8-jdk-alpine

CMD ["gradle"]

ENV GRADLE_HOME /opt/gradle
ENV GRADLE_VERSION 4.5

ARG GRADLE_DOWNLOAD_SHA256=03f2a43a314ff0fb843a85ef68078e06d181c4549c1e5fb983f289382b59b5e3

#Download kubectl
ADD https://storage.googleapis.com/kubernetes-release/release/v1.9.2/bin/linux/amd64/kubectl /usr/local/bin/kubectl

RUN set -o errexit -o nounset \
	&& echo "Installing build dependencies" \
	#kubectl BEGIN
	&& set -x \
    	&& apk add --no-cache curl ca-certificates \
    	&& chmod +x /usr/local/bin/kubectl \
	#kubectl END
	&& apk add --no-cache --virtual .build-deps \
		ca-certificates \
		openssl \
		unzip \
		#Install docker
		docker \
	\
	&& echo "Downloading Gradle" \
	&& wget -O gradle.zip "https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip" \
	\
	&& echo "Checking download hash" \
	&& echo "${GRADLE_DOWNLOAD_SHA256} *gradle.zip" | sha256sum -c - \
	\
	&& echo "Installing Gradle" \
	&& unzip gradle.zip \
	&& rm gradle.zip \
	&& mkdir /opt \
	&& mv "gradle-${GRADLE_VERSION}" "${GRADLE_HOME}/" \
	&& ln -s "${GRADLE_HOME}/bin/gradle" /usr/bin/gradle \
	\
	&& apk del .build-deps \
	\
	&& echo "Adding gradle user and group" \
	&& addgroup -S -g 1000 gradle \
	&& adduser -D -S -G gradle -u 1000 -s /bin/ash gradle \
	&& mkdir /home/gradle/.gradle \
	&& chown -R gradle:gradle /home/gradle \
	\
	&& echo "Symlinking root Gradle cache to gradle Gradle cache" \
	&& ln -s /home/gradle/.gradle /root/.gradle

# Create Gradle volume
USER gradle
VOLUME "/home/gradle/.gradle"
WORKDIR /home/gradle

RUN set -o errexit -o nounset \
	&& echo "Testing Gradle installation" \
	&& gradle --version

USER root
