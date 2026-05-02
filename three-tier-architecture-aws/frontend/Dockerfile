FROM nginx:latest

# Copy your frontend static files (html, css, js)
COPY . /usr/share/nginx/html/

# OVERWRITE the default config with your fixed nginx.conf
# This is the most critical line for the permanent fix
#COPY nginx.conf /etc/nginx/conf.d/default.conf
 COPY nginx.conf /etc/nginx/templates/default.conf.template

EXPOSE 80
