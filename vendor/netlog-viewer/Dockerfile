# Build a lazycat-installable image that serves Chromium's Net Log Viewer
# (https://netlog-viewer.appspot.com/) as a static site.
#
# We don't bundle catapult into this repo — the upstream is huge. Instead the
# builder stage sparse-clones only the directories netlog_viewer needs:
#   netlog_viewer/                 — the app itself (HTML / JS / CSS)
#   third_party/polymer            — Polymer 1 + webcomponentsjs polyfill
#   third_party/polymer-svg-template
#   third_party/jszip              — used by import_view.js for .zip NetLogs
#
# We deliberately serve the dev layout (same as catapult's
# `netlog_viewer/bin/serve_static`), not the appspot vulcanized.html. Reasons:
#   - vulcanize is deprecated upstream and finicky to install on modern node.
#   - The static file count is small (~few hundred), nginx handles it fine.
#   - HTML imports still work via webcomponentsjs polyfill that the upstream
#     index.html already loads — same code path as the live appspot site.

FROM alpine:3.20 AS fetcher

ARG CATAPULT_REF=main
ENV CATAPULT_REF=${CATAPULT_REF}

RUN apk add --no-cache git ca-certificates

WORKDIR /src
RUN git clone --depth=1 --filter=blob:none --sparse \
      --branch "${CATAPULT_REF}" \
      https://chromium.googlesource.com/catapult.git catapult \
 && cd catapult \
 && git sparse-checkout init --cone \
 && git sparse-checkout set \
      netlog_viewer \
      third_party/polymer \
      third_party/polymer-svg-template \
      third_party/jszip \
 && rm -rf .git

FROM nginx:1.27-alpine

# Match catapult/netlog_viewer/bin/serve_static layout:
#   netlog_viewer/                  -> /
#   third_party/polymer/components/ -> /components
#   third_party/                    -> /third_party  (jszip, polymer-svg-template)
COPY --from=fetcher /src/catapult/netlog_viewer/netlog_viewer/ /usr/share/nginx/html/
COPY --from=fetcher /src/catapult/third_party/polymer/components/ /usr/share/nginx/html/components/
COPY --from=fetcher /src/catapult/third_party/ /usr/share/nginx/html/third_party/

COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
  CMD wget -qO- http://127.0.0.1/healthz >/dev/null 2>&1 || exit 1
