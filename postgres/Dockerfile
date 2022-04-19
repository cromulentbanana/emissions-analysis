FROM ubuntu:20.04
LABEL maintainer="Dan Levin <dan@badpacket.in>"

ENV DEBIAN_FRONTEND noninteractive
ENV LANGUAGE en_US.utf8
ENV LANG=C.UTF-8
ENV LC_CTYPE en_US.utf8
ENV LC_NUMERIC en_US.utf8
ENV LC_TIME en_US.utf8
ENV LC_COLLATE en_US.utf8
ENV LC_MONETARY en_US.utf8
ENV LC_MESSAGES en_US.utf8
ENV LC_PAPER en_US.utf8
ENV LC_NAME en_US.utf8
ENV LC_ADDRESS en_US.utf8
ENV LC_TELEPHONE en_US.utf8
ENV LC_MEASUREMENT en_US.utf8
ENV LC_IDENTIFICATION en_US.utf8
ENV LC_ALL=C.UTF-8

# FIXME, having to copy the requirements into the container is a pain and leads
# to two sources of truth.
#hadolint ignore=DL3008,DL3015,DL3059
RUN apt-get update \
    && apt-get -y install curl postgresql \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

USER postgres

# Create a PostgreSQL role named ``docker`` with ``docker`` as the password and
# then create a database `docker` owned by the ``docker`` role.
# Note: here we use ``&&\`` to run commands one after the other - the ``\``
#       allows the RUN command to span multiple lines.
RUN    /etc/init.d/postgresql start &&\
    psql --command "CREATE USER demo WITH SUPERUSER PASSWORD 'demo';" &&\
    createdb -O demo demo &&\
    /etc/init.d/postgresql stop

# Adjust PostgreSQL configuration so that remote connections to the
# database are possible.
RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/12/main/pg_hba.conf && \
    echo "listen_addresses='*'" >> /etc/postgresql/12/main/postgresql.conf

# Expose the PostgreSQL port
EXPOSE 5432

# Add VOLUMEs to allow backup of config, logs and databases
# VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]

# Set the default command to run when starting the container
CMD ["/usr/lib/postgresql/12/bin/postgres", "-D", "/var/lib/postgresql/12/main", "-c", "config_file=/etc/postgresql/12/main/postgresql.conf"]
