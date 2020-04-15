# build environment
FROM node:12.2.0-alpine as build

# install requirements for canvas
RUN apk add --no-cache \
    sudo \
    curl \
    build-base \
    g++ \
    libpng \
    libpng-dev \
    jpeg-dev \
    pango-dev \
    cairo-dev \
    giflib-dev \
    python \
    ;

WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH
COPY package.json /app/package.json
RUN npm install --silent --force
RUN npm install react-scripts@^3.3.0 -g --silent

# install canvas
RUN apk --no-cache add ca-certificates wget  && \
    wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub && \
    wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.29-r0/glibc-2.29-r0.apk && \
    apk add glibc-2.29-r0.apk && \
    npm install canvas@2.5.0

COPY . /app
RUN npm run build

# production environment
FROM nginx:1.16.0-alpine

# copy letsencrypt cert
RUN mkdir /etc/letsencrypt
COPY letsencrypt/live/socialcleanr.ie/fullchain.pem /etc/letsencrypt
COPY letsencrypt/live/socialcleanr.ie/privkey.pem /etc/letsencrypt

COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
EXPOSE 443

CMD ["nginx", "-g", "daemon off;"]
