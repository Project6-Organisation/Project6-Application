FROM node as build

RUN echo "=== build ==="

WORKDIR /app

COPY . .

RUN printf 'export const API_BASE_URL = "%s";\n' "$API_BASE_URL" > src/app/config.ts

RUN npm ci \
    && npx @angular/cli build --optimization

FROM alpine:3.19 as front

RUN echo "=== runtime ==="

WORKDIR /app

# create a group "appgroup" and a user "appuser"
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

COPY --from=build /app/dist/microcrm/browser /app/front
COPY misc/docker/Caddyfile /app/Caddyfile

RUN apk add caddy openssl

RUN mkdir -p /home/appuser/.local/share/caddy \
    && mkdir -p /home/appuser/.config/caddy \
    && chown -R appuser:appgroup /home/appuser \
    && chown -R appuser:appgroup /app

RUN mkdir -p /app/certs \
    && openssl req -x509 -nodes -newkey rsa:2048 \
      -days 365 \
      -keyout /app/certs/key.pem \
      -out /app/certs/cert.pem \
      -subj "/CN=localhost" \
    && chown -R appuser:appgroup /app

USER appuser

EXPOSE 80
EXPOSE 443

CMD ["/usr/sbin/caddy", "run"]