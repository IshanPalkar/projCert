# Use PHP-FPM official image
FROM php:8.2-fpm

# Install Nginx and dependencies
RUN apt-get update && apt-get install -y \
    nginx \
    curl \
    git \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# Copy website files
COPY . /var/www/html/

# Remove default Nginx config
RUN rm /etc/nginx/sites-enabled/default

# Copy custom Nginx config
COPY default.conf /etc/nginx/conf.d/

# Expose port 80
EXPOSE 80

# Start PHP-FPM and Nginx
CMD service php8.2-fpm start && nginx -g 'daemon off;'

