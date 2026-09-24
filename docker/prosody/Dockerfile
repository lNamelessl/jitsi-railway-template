FROM ghcr.io/jitsi/prosody:stable-11248

# Railway mounts volumes root-owned; prosody's boot check requires /var/lib/prosody
# writable by the container user (s6, uid 1000). s6-overlay's fix-attrs oneshot runs
# as root at boot and chowns the volume root, using the legacy /etc/fix-attrs.d path
# (do NOT put plain files under /etc/s6-overlay/s6-rc.d/fix-attrs.d - that directory
# is an s6-rc service directory and a stray file aborts the whole service compile).
USER root
RUN mkdir -p /etc/fix-attrs.d \
 && printf '/var/lib/prosody false s6 s6 0777\n' > /etc/fix-attrs.d/01-prosody-data
USER s6
