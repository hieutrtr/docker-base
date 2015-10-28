FROM centos:6.7
MAINTAINER Hieu Tran Trung <hieutrantrung.it@gmail.com>

# Ensure we create the cluster with UTF-8 locale
RUN yum reinstall -y glibc-common
RUN localedef -i en_US -c -f UTF-8 en_US.UTF-8

# Install EPEL6 for additional packages
RUN rpm -Uvh http://mirror.pnl.gov/epel/6/i386/epel-release-6-8.noarch.rpm

# Now install postgreSQL 9.4
RUN rpm -Uvh http://yum.postgresql.org/9.4/redhat/rhel-6-x86_64/pgdg-centos94-9.4-2.noarch.rpm

# Install postgre app
RUN yum install -y postgresql94 postgresql94-server postgresql94-contrib

# Install other tools.
RUN yum install -y inotify-tools vi which

# initialize DB data files
RUN su - postgres -c /usr/pgsql-9.4/bin/initdb

# Cofigure the database to use our data dir.

## all for all
RUN echo "host    all             all             0.0.0.0/0            trust" >> /var/lib/pgsql/9.4/data/pg_hba.conf
RUN echo "listen_addresses='*'" >> /var/lib/pgsql/9.4/data/postgresql.conf

# now prepare sshd and supervisor
RUN yum install -y openssh-server supervisor

RUN mkdir -p /var/run/sshd
RUN mkdir -p /var/log/supervisor

RUN ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_dsa_key && ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key

RUN echo 'root:docker' |chpasswd

ADD supervisord.conf /etc/

VOLUME ["/data"]

ADD scripts /scripts
RUN chmod +x /scripts/initdb.sh && ln -s /scripts/initdb.sh /initdb

EXPOSE 22 5432

# start the database
#CMD service postgresql-9.3 start
CMD ["/usr/bin/supervisord"]

# Expose our data, log, and configuration directories.
#VOLUME ["/data", "/var/log/postgresql", "/etc/postgresql"]
