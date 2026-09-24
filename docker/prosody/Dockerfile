FROM ghcr.io/jitsi/prosody:stable-11248

# Railway mounts volumes root-owned. The upstream image ends with `USER s6`, so
# s6-overlay drops privileges before fix-attrs and its chown fails (EPERM) on the
# root-owned volume. Run the container as root (Railway norm) and let fix-attrs
# chown the volume root to s6 (uid 1000) at every boot so prosody's writable
# check passes and account data persists on the volume.
USER root
RUN mkdir -p /etc/fix-attrs.d \
 && printf '/var/lib/prosody false s6 s6 0755\n' > /etc/fix-attrs.d/01-prosody-data
