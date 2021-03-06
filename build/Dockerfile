FROM yeetzone/gosu
MAINTAINER YEET Zone <support@yeet.zone>

# Set build arguments.
ARG STEAM_USER
ENV STEAM_USER ${STEAM_USER:-"steam"}
ARG STEAM_HOME
ENV STEAM_HOME ${STEAM_HOME:-"/opt/steam"}
ARG STEAM_URL
ENV STEAM_URL ${STEAM_URL:-"https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz"}

# Add our user/group first to ensure their IDs get set consistently.
RUN set -ex \
	&& groupadd -g 1001 -r $STEAM_USER \
	&& useradd -u 1001 -rm -d $STEAM_HOME -g $STEAM_USER $STEAM_USER

# Install everything.
RUN set -ex \
	# Install dependencies.
	&& apt-get update \
	&& apt-get install -y --no-install-recommends wget ca-certificates lib32gcc1 \
	# Install SteamCMD.
	&& wget -qO- $STEAM_URL | gosu $STEAM_USER tar -xzvC $STEAM_HOME \
	&& gosu $STEAM_USER $STEAM_HOME/steamcmd.sh \
		+@ShutdownOnFailedCommand 1 \
		+quit \
	&& rm -rf $STEAM_HOME/Steam/logs $STEAM_HOME/Steam/appcache/httpcache \
	&& find $STEAM_HOME/package -type f ! -name "steam_cmd_linux.installed" ! -name "steam_cmd_linux.manifest" -delete \
	# Clean up.
	&& apt-get purge -y --auto-remove wget \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Copy scripts.
COPY /script/steamcmd /usr/local/bin/steamcmd

# Copy entrypoint script.
COPY /entrypoint.sh /

# Set permissions.
RUN set -ex \
	&& chmod +x /entrypoint.sh \
	&& chmod +x /usr/local/bin/steamcmd

# Set entrypoint.
ENTRYPOINT ["/entrypoint.sh"]

# Set command.
CMD ["steamcmd"]
