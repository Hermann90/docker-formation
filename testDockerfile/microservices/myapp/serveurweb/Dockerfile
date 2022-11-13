FROM centos:7
RUN yum update
RUN yum install -y nginx
VOLUME /var/www/html/
ENTRYPOINT ["nginx", "-g", "daemon off;"]