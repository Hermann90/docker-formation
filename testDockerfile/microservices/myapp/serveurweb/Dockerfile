FROM centos:7
RUN yum update -y
RUN yum install -y nginx
VOLUME /var/www/html/
ENTRYPOINT ["nginx", "-g", "daemon off;"]