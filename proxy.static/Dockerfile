# set the base image
# n/b: for production, node is only used for building 
# the static Html and javascript files
# as react creates static html and js files after build
# these are what will be served by nginx
# use alias build to be easier to refer this container elsewhere
# e.g inside nginx container
FROM node:alpine as build
# set working directory
# this is the working folder in the container
# from which the app will be running from

WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH
# copy everything to /app directory
# as opposed to on dev, in prod everything is copied to docker
COPY package.json ./
COPY package-lock.json ./
# add the node_modules folder to $PATH

# install and cache dependencies
RUN npm ci 
COPY . .

# set argument for api version
ARG REACT_APP_API_VERSION=v1
# set argument for api url address
ARG REACT_APP_API_URL=http://localhost

RUN echo $REACT_APP_API_VERSION
RUN echo $REACT_APP_API_URL

# build client with arguments
RUN REACT_APP_API_VERSION=$REACT_APP_API_VERSION REACT_APP_API_URL=$REACT_APP_API_URL npm run build


FROM nginx:alpine

COPY /web/nginx.conf ./etc/nginx/nginx.conf
RUN mkdir /etc/nginx/conf.d
RUN mkdir /etc/nginx/includes


## Remove default nginx index page
RUN rm -rf /usr/share/nginx/html/*

COPY --from=build /app/build /usr/share/nginx/html

RUN ls /usr/share/nginx/html


EXPOSE 80 433
#build the project for production
CMD ["nginx", "-g", "daemon off;"]
