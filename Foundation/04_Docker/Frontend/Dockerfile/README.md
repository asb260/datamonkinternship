<img width="1335" height="187" alt="Screenshot from 2025-09-26 19-22-35" src="https://github.com/user-attachments/assets/d971ddf2-8990-4920-90f2-c271ef3f4d3f" />
FROM nginx:alpine
# Remove default Nginx welcome page
COPY nginx.conf /etc/nginx/conf.d/default.conf
# Copy your index.html into the Nginx content directory
COPY index.html /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

