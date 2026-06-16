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

COPY --from=build /frontend/dist/ /frontend/

EXPOSE 80