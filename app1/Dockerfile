FROM almalinux:8
RUN yum install nginx -y
RUN rm -rf /usr/share/nginx/html/index.html
RUN echo "<h1> hello this is from app1 </h1>" > /usr/share/nginx/html/hello.html
# daemon off command makes our container running and attached to screen
CMD [ "nginx", "-g", "daemon off;" ]