# Using already cached version.
FROM owasp/modsecurity:3.0.4

LABEL maintainer="Chaim Sanders <chaim.sanders@gmail.com>"

ENV PARANOIA=1 \
    ANOMALY_INBOUND=5 \
    ANOMALY_OUTBOUND=4 \
    NGINX_KEEPALIVE_TIMEOUT=60s \
    ERRORLOG=/var/log/nginx/error.log \
    LOGLEVEL=warn \
    USER=nginx \
    PORT=80 \
    SERVERNAME=locahost \
    WORKER_CONNECTIONS=1024 \
    MODSEC_RULE_ENGINE=on \
    MODSEC_REQ_BODY_ACCESS=on \
    MODSEC_REQ_BODY_LIMIT=13107200 \
    MODSEC_REQ_BODY_NOFILES_LIMIT=131072 \
    MODSEC_RESP_BODY_ACCESS=on \
    MODSEC_RESP_BODY_LIMIT=1048576 \
    MODSEC_PCRE_MATCH_LIMIT=100000 \
    MODSEC_PCRE_MATCH_LIMIT_RECURSION=100000

RUN apt-get update && \
    apt-get -y install \
      ca-certificates \
      git \
      iproute2 \
      moreutils

WORKDIR /opt/owasp-crs

ARG CRS_REPO=coreruleset/coreruleset
ARG CRS_VERSION=v3.0/master
RUN git clone -b "${CRS_VERSION}" "https://github.com/${CRS_REPO}" .

RUN mv -v crs-setup.conf.example crs-setup.conf
RUN ln -sv /opt/owasp-crs /etc/modsecurity.d/

COPY 3.3-nginx/docker-entrypoint.sh /

RUN ls /etc/nginx/conf.d/
RUN cat /etc/nginx/conf.d/default.conf

# This messes up proxy configuration.
COPY 3.3-nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf

COPY 3.3-nginx/nginx.conf /etc/nginx/nginx.conf
COPY src/opt/modsecurity/activate-rules.sh /opt/modsecurity/
COPY src/etc/modsecurity.d/*.conf /etc/modsecurity.d/

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
