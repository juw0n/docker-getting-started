# syntax=docker/dockerfile:1
# Docker look for or download the node18-alpine to be use as base image
FROM node:18-alpine
# Docker create /app as the working dir
WORKDIR /app
# copy the app files into the workdir(/app) 
COPY . .
# Docker use yarn to install the application's dependencies
RUN yarn install --production
# The CMD directive specifies the default command to run when starting a container from this image.
CMD [ "node", "src/index.js" ]
# the app will be expose at port 3000
EXPOSE 3000