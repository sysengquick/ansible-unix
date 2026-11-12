FROM python:3.13-slim

# set useful environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONDONTWRITEBYTECODE=1
ENV PIP_NO_CACHE_DIR=1

# install required packages
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    bash-completion \
    iproute2 \
    iputils-ping \
    git \
    less \
    locales \
    openssh-client \
    sshpass \
  && rm -rf /var/lib/apt/lists/*

# add en_US.UTF-8 locales
RUN sed -i 's/^# *en_US.UTF-8/en_US.UTF-8/' /etc/locale.gen \
  && locale-gen en_US.UTF-8

# install ansible packages
RUN python3 -m pip install 'ansible-core>=2.22,<2.23' ansible-lint

# install the collection dependencies declared in galaxy.yml
COPY collections/ansible_collections/sysengquick/unix/galaxy.yml /tmp/galaxy.yml
RUN python3 -c "import subprocess, yaml; deps = yaml.safe_load(open('/tmp/galaxy.yml'))['dependencies']; subprocess.run(['ansible-galaxy', 'collection', 'install', *(f'{n}:{v}' for n, v in deps.items())], check=True)" \
  && rm -f /tmp/galaxy.yml

# copy all profile.d scripts
COPY .devcontainer/files/etc/profile.d/*.sh /etc/profile.d/

# source all profile.d scripts in bashrc
RUN echo 'for script in /etc/profile.d/*.sh; do . "$script"; done' >> /etc/bash.bashrc

# use bash instead of python as our default command
CMD ["/bin/bash"]
