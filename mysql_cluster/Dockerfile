FROM centos:centos6
MAINTAINER fanteathy "517081935@qq.com"

RUN yum install -y mysql-server mysql

RUN /etc/init.d/mysqld start &&\  
    mysql -e "grant all privileges on *.* to 'fanteathy'@'%' identified by 'fanteathy';"&&\  
    mysql -e "grant all privileges on *.* to 'fanteathy'@'localhost' identified by 'fanteathy';"&&\  
    mysql -u fanteathy -pfanteathy -e "show databases;"

EXPOSE 3306  
   
CMD ["/usr/bin/mysqld_safe"]
