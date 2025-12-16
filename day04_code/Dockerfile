# Use official Nginx image as base
FROM nginx:latest

# Set working directory inside container
WORKDIR /usr/share/nginx/html

# Copy all files from host to container
COPY . .

# Expose port 80
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]

