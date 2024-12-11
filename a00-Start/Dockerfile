# Use node:16 as base Docker Image
FROM node:16

# Copy local files
COPY . .

# Execute dependencies installation
RUN npm install

# Execute build
RUN npm run build

# Nginx installation
RUN apt-get update
RUN apt-get install -y curl wget git nginx

# Copy artifacts
ADD dist /usr/share/nginx/html
ADD nginx.conf /etc/nginx/conf.d/
RUN rm -f /etc/nginx/conf.d/default.conf

# Expose container ports
EXPOSE 22 80 8080

# Execute the command to run the application
CMD ["sh", "-c", "npm run start & nginx -g 'daemon off;'"]
