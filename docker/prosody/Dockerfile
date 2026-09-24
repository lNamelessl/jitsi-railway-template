FROM ghcr.io/jitsi/prosody:stable-11248

# Railway mounts volumes root-owned; prosody's boot check requires /var/lib/prosody
# writable by the container user (s6, uid 1000). Register an s6-overlay fix-attrs
# entry so the volume root is chowned to s6 on every boot (runs as root in preinit).
USER root
RUN mkdir -p /etc/fix-attrs.d /etc/s6-overlay/s6-rc.d/fix-attrs.d \
 && printf '/var/lib/prosody false s6 s6 0777\n' > /etc/fix-attrs.d/01-prosody-data \
 && printf '/var/lib/prosody false s6 s6 0777\n' > /etc/s6-overlay/s6-rc.d/fix-attrs.d/01-prosody-data
USER s6
