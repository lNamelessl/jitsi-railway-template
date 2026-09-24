FROM ghcr.io/coturn/coturn:4.18.0-r0-alpine

# The turnserver config must be rendered from real environment variables
# (TURN_SHARED_SECRET / REALM). Railway start commands receive no shell
# expansion, so a tiny entrypoint wrapper does the rendering, then execs
# turnserver in the foreground. No listening-ip/relay-ip overrides: coturn
# auto-detects the container addresses (0.0.0.0 is rejected by coturn).
COPY entrypoint.sh /usr/local/bin/railway-coturn-entrypoint.sh
RUN chmod +x /usr/local/bin/railway-coturn-entrypoint.sh
ENTRYPOINT ["/usr/local/bin/railway-coturn-entrypoint.sh"]
