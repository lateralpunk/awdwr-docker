FROM ubuntu:18.04

ENV RVM_VERSION 1.29.9
ENV RUBY_VERSION 1.8.7-p302
ENV GEMS_VERSION 1.4.2
ENV RAKE_VERSION 0.8.7
ENV RAILS_VERSION 2.2.2
ENV SQLITE3_RUBY_VERSION 1.3.2

# Update 
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install locales curl gpg sudo -y \
    && apt-get install patch gawk g++ gcc autoconf automake bison libc6-dev libffi-dev libgdbm-dev libncurses5-dev libsqlite3-dev libtool libyaml-dev make patch pkg-config sqlite3 zlib1g-dev libgmp-dev libreadline-dev libssl1.0-dev -y \
    && rm -rf /var/lib/apt/lists/*

# Setup User
RUN useradd --home /home/app -M app -K UID_MIN=10000 -K GID_MIN=10000 -s /bin/bash \
    && mkdir /home/app \
    && chown app:app /home/app \
    && adduser app sudo \
    && echo 'app ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER app

# Install RVM
RUN for server in $(shuf -e hkp://pool.sks-keyservers.net \
                          hkp://ipv4.pool.sks-keyservers.net \
                          hkp://pgp.mit.edu \
                          hkp://keyserver.pgp.com) ; do \
      gpg --keyserver "$server" --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB && break || : ; \
  done
RUN \curl -sSL https://get.rvm.io | bash -s $RVM_VERSION

# Install Ruby
RUN /bin/bash -l -c 'rvm install $RUBY_VERSION'
RUN /bin/bash -l -c 'rvm use $RUBY_VERSION --default'
RUN /bin/bash -l -c 'gem uninstall -i ~/.rvm/gems/$RUBY_VERSION@global rake'
RUN /bin/bash -l -c 'gem update --system $GEMS_VERSION'
RUN /bin/bash -l -c 'gem install rake -v $RAKE_VERSION'
RUN /bin/bash -l -c 'gem install rails -v $RAILS_VERSION'
RUN /bin/bash -l -c 'gem install sqlite3-ruby -v $SQLITE3_RUBY_VERSION'

WORKDIR /home/app

RUN /bin/bash -l -c 'rvm cleanup all'
RUN sudo apt-get remove curl gpg patch gawk g++ gcc autoconf automake bison libc6-dev libffi-dev libgdbm-dev libncurses5-dev libtool libyaml-dev make patch pkg-config zlib1g-dev libgmp-dev libreadline-dev -y
RUN sudo apt autoremove -y
