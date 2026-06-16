FROM node:24.13.0-alpine AS build

ARG BASE_URL=/
ARG VUE_APP_API_URL=/api/
ARG NODE_ENV=development

ENV BASE_URL=$BASE_URL
ENV VUE_APP_API_URL=$VUE_APP_API_URL
ENV NODE_ENV=$NODE_ENV

ENV NODE_OPTIONS=--openssl-legacy-provider

WORKDIR /frontend


COPY package.json package-lock.json ./

RUN npm ci
COPY . .
RUN npm run build

FROM nginx:1.30-alpine3.23-slim

ARG UID=1001

COPY --from=build /frontend/dist/ /frontend/

RUN apk add --no-cache curl
RUN adduser -S -u ${UID} -G nginx frontend

EXPOSE 80

USER frontend

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 CMD curl -fsS http://127.0.0.1 || exit 1