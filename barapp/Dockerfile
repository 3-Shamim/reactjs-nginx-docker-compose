# set the base image
# n/b: for production, node is only used for building 
# the static Html and javascript files
# as react creates static html and js files after build
# these are what will be served by nginx
# use alias build to be easier to refer this container elsewhere
# e.g inside nginx container

FROM node:12.8.1-alpine as build

# set working directory
# this is the working folder in the container
# from which the app will be running from
WORKDIR /app

# copy everything to /app directory
COPY . ./

# add the node_modules folder to $PATH
# ENV PATH /app/node_modules/.bin:$PATH

# --no-cache: download package index on-the-fly, no need to cleanup afterwards
# --virtual: bundle packages, remove whole bundle at once, when done. This is for alpine version

# RUN apk --no-cache --virtual build-dependencies add \
#     python \
#     make \
#     g++ \
#     && npm install \
#     && apk del build-dependencies

# install and cache dependencies
RUN npm install

#build the project for production
RUN npm run build

# set up production environment
# the base image for this is an alpine based nginx image
FROM nginx:alpine

# copy the build folder from react to the root of nginx (www)
COPY --from=build /app/build /usr/share/nginx/html

# --------- only for those using react router ----------
# if you are using react router 
# you need to overwrite the default nginx configurations
# remove default nginx configuration file
RUN rm /etc/nginx/conf.d/default.conf

# replace with custom one
COPY nginx/conf/conf.d /etc/nginx/conf.d
COPY nginx/ssl /etc/nginx/ssl
# --------- /only for those using react router ----------

# expose port 80 to the outer world
EXPOSE 80 443

# start nginx 
CMD ["nginx", "-g", "daemon off;"]