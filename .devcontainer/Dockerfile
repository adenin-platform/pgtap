# Use the official Ubuntu base image
FROM mcr.microsoft.com/vscode/devcontainers/base:1-ubuntu-24.04

# Install dependencies
RUN apt-get update && apt-get install -y \
    wget \
    ca-certificates \
    gnupg \
    lsb-release

# Add PostgreSQL official APT repository
RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /usr/share/keyrings/postgresql-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/postgresql-keyring.gpg] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list

# Update package lists and install PostgreSQL 17 and development tools
RUN apt-get update && apt-get install -y \
    postgresql-17 \
    postgresql-server-dev-17 \
    postgresql-contrib-17 \
    postgresql-client-17 \
    make \
    build-essential \
    perl \
    patch \
    git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Set up PostgreSQL
USER postgres
RUN /etc/init.d/postgresql start && \
    psql --command "CREATE USER vscode WITH SUPERUSER PASSWORD 'vscode';" && \
    createdb -O vscode vscode

USER root

# Ensure PostgreSQL service is configured to allow connections
RUN echo "listen_addresses = '*'" >> /etc/postgresql/17/main/postgresql.conf
RUN echo "host all all 0.0.0.0/0 md5" >> /etc/postgresql/17/main/pg_hba.conf
RUN echo "local all all trust" >> /etc/postgresql/17/main/pg_hba.conf

# Set up environment
ENV PATH="/usr/lib/postgresql/17/bin:$PATH"
ENV PGDATA="/var/lib/postgresql/17/main"

# Switch back to the vscode user
USER vscode

# Set the working directory
WORKDIR /workspace