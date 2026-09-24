FROM ghcr.io/jitsi/prosody:stable-11248

# Railway mounts volumes root-owned (uid 0), while the upstream image ends with
# `USER s6`, so s6-overlay drops privileges before its own fix-attrs chown can run
# (EPERM) and prosody's writable check aborts the boot. Railway containers also
# cannot chmod/chown a root-owned volume as non-root, and prosody refuses to run
# as root ("Danger, Will Robinson"). Strategy:
#   1. boot as root, so s6-overlay's fix-attrs chowns the volume root to s6 (1000)
#   2. run the prosody daemon itself as s6 (upstream refuses root)
#   3. at the end of the boot-time config render, chown the rendered runtime tree
#      (/run/prosody/config incl. self-signed certs, and the data dir) to s6 so the
#      s6-process prosody can read/write everything (accounts persist on the volume).
USER root
RUN mkdir -p /etc/fix-attrs.d \
 && printf '/var/lib/prosody false s6 s6 0755\n' > /etc/fix-attrs.d/01-prosody-data \
 && sed -i 's|^exec prosody|exec s6-setuidgid s6 prosody|' /etc/s6-overlay/scripts/prosody \
 && sed -i '$a chown -R s6:s6 /run/prosody/config "$PROSODY_DATA_DIR" 2>/dev/null || true' /etc/s6-overlay/scripts/config
