FROM ghcr.io/jitsi/prosody:stable-11248

# Railway mounts volumes root-owned (uid 0), while the upstream image ends with
# `USER s6`, so s6-overlay drops privileges before its own fix-attrs chown can run
# (EPERM) and prosody's writable check aborts the boot. Railway containers also
# cannot chmod/chown a root-owned volume as non-root, and prosody refuses to run
# as root ("Danger, Will Robinson"). Strategy:
#   1. boot as root, so s6-overlay's fix-attrs chowns the volume root to s6 (1000)
#   2. generate self-signed certs as s6 (prosodyctl cert generate fails as root),
#      then chown the rendered runtime tree and data dir to s6
#   3. run the prosody daemon itself as s6 (upstream refuses root)
USER root
RUN mkdir -p /etc/fix-attrs.d \
 && printf '/var/lib/prosody false s6 s6 0755\n' > /etc/fix-attrs.d/01-prosody-data \
 && sed -i 's|^exec prosody|exec s6-setuidgid s6 prosody|' /etc/s6-overlay/scripts/prosody \
 && sed -i 's#| prosodyctl #| /command/s6-setuidgid s6 prosodyctl #g' /etc/s6-overlay/scripts/config \
 && sed -i '$a chown s6:s6 /run/prosody 2>/dev/null || true && chown -R s6:s6 /run/prosody/config "$PROSODY_DATA_DIR" 2>/dev/null || true' /etc/s6-overlay/scripts/config
