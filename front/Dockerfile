FROM node as build

WORKDIR /app

COPY . .

RUN npm ci \
    && npx @angular/cli build --optimization

FROM alpine:3.19 as front

WORKDIR /app

# create a group "appgroup" and a user "appuser"
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

COPY --from=build /app/dist/microcrm/browser /app/front
COPY misc/docker/Caddyfile /app/Caddyfile

RUN apk add caddy

RUN mkdir -p /home/appuser/.local/share/caddy \
    && mkdir -p /home/appuser/.config/caddy \
    && chown -R appuser:appgroup /home/appuser \
    && chown -R appuser:appgroup /app

USER appuser

EXPOSE 80
EXPOSE 443

CMD ["/usr/sbin/caddy", "run"]