FROM alpine:latest as builder 
LABEL maintainer="mail@sebakrau.de"

# To reduce image size all build and cleanup steps are performed in one docker layer.
RUN \
  echo "# INSTALL DEPENDENCIES ##########################################" && \
  apk --no-cache add curl g++ gcc git make && \
  mkdir -p /tmp/build && \
  echo "# FETCH INSTALLATION FILES ######################################" && \
  curl https://raw.githubusercontent.com/ioquake/ioq3/master/misc/linux/server_compile.sh -o /tmp/build/compile.sh && \
  curl https://ioquake3.org/data/quake3-latest-pk3s.zip --referer https://ioquake3.org/extras/patch-data/ -o /tmp/build/quake3-latest-pk3s.zip && \
  echo "# NOW THE INSTALLATION ##########################################" && \
  echo "y" | sh /tmp/build/compile.sh && \
  unzip /tmp/build/quake3-latest-pk3s.zip -d /tmp/build/ && \
  cp -r /tmp/build/quake3-latest-pk3s/* ~/ioquake3

FROM alpine:latest 
# Use a dedicated user to run the server.
RUN adduser ioq3srv -D
# Copy the build results to users home directory. Make sure ioq3srv is the owner.
COPY --chown=ioq3srv --from=builder /root/ioquake3 /home/ioq3srv/ioquake3
# Switch to user to make sure
USER ioq3srv
# Create the q3 user data directory. Thus, ioq3srv is the owner.
RUN mkdir -p ~/.q3a/baseq3
EXPOSE 27960/udp
ENTRYPOINT ["/home/ioq3srv/ioquake3/ioq3ded.x86_64"]