FROM ubuntu:18.04 as build

# https://superuser.com/questions/1160025/how-to-solve-ttyname-failed-inappropriate-ioctl-for-device-in-vagrant#comment2135857_1160074
RUN sed -i -e 's/mesg n .*true/tty -s \&\& mesg n/g' /root/.profile

# Update 
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install locales curl gpg sudo wget -y \
    && apt-get install patch gawk g++ gcc autoconf automake bison libc6-dev libffi-dev libgdbm-dev libncurses5-dev libsqlite3-dev libtool libyaml-dev make patch pkg-config sqlite3 zlib1g-dev libgmp-dev libreadline-dev libssl1.0-dev -y \
    && rm -rf /var/lib/apt/lists/*

ENV RVM_VERSION 1.29.9
# oldest compatible version
# ENV RUBY_VERSION 1.8.7-p302
ENV RUBY_VERSION 1.8.7-p374
ENV GEMS_VERSION 1.4.2
ENV RAKE_VERSION 0.8.7
ENV RAILS_VERSION 2.2.2
ENV SQLITE3_RUBY_VERSION 1.3.2

# Install RVM
RUN for server in $(shuf -e hkp://pool.sks-keyservers.net \
                          hkp://ipv4.pool.sks-keyservers.net \
                          hkp://pgp.mit.edu \
                          hkp://keyserver.pgp.com) ; do \
      gpg --keyserver "$server" --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB && break || : ; \
  done
RUN \curl -sSL https://get.rvm.io | sudo bash -s $RVM_VERSION

# Install Ruby
RUN /bin/bash -l -c 'rvm install $RUBY_VERSION'
RUN /bin/bash -l -c 'rvm use $RUBY_VERSION --default'
RUN /bin/bash -l -c 'gem uninstall -i /usr/local/rvm/gems/$RUBY_VERSION@global rake'
RUN /bin/bash -l -c 'gem update --system $GEMS_VERSION'
RUN /bin/bash -l -c 'gem install rake -v $RAKE_VERSION'
RUN /bin/bash -l -c 'gem install rails -v $RAILS_VERSION'
RUN /bin/bash -l -c 'gem install sqlite3-ruby -v $SQLITE3_RUBY_VERSION'
RUN /bin/bash -l -c 'rvm cleanup all'

RUN cd tmp \
    && wget http://launchpadlibrarian.net/413030694/libssl1.0.0_1.0.2n-1ubuntu5.3_amd64.deb \
    && wget http://launchpadlibrarian.net/413030689/libssl1.0-dev_1.0.2n-1ubuntu5.3_amd64.deb \
    && wget http://mirrors.kernel.org/ubuntu/pool/main/r/readline/libreadline-dev_7.0-3_amd64.deb \
    && wget http://mirrors.kernel.org/ubuntu/pool/main/r/readline/libreadline7_7.0-3_amd64.deb \
    && wget http://mirrors.kernel.org/ubuntu/pool/main/r/readline/readline-common_7.0-3_all.deb \
    && wget http://mirrors.kernel.org/ubuntu/pool/main/n/ncurses/libtinfo5_6.1-1ubuntu1_amd64.deb \
    && wget http://mirrors.kernel.org/ubuntu/pool/main/n/ncurses/libtinfo-dev_6.1-1ubuntu1_amd64.deb \
    && wget http://security.ubuntu.com/ubuntu/pool/main/s/sqlite3/sqlite3_3.22.0-1ubuntu0.3_amd64.deb \
    && wget http://security.ubuntu.com/ubuntu/pool/main/s/sqlite3/libsqlite3-0_3.22.0-1ubuntu0.3_amd64.deb

FROM ubuntu:20.04 as prod

COPY --from=build /tmp /tmp

RUN sed -i -e 's/mesg n .*true/tty -s \&\& mesg n/g' /root/.profile

RUN cd tmp \
    && dpkg -i libssl1.0.0_1.0.2n-1ubuntu5.3_amd64.deb \
    && dpkg -i libssl1.0-dev_1.0.2n-1ubuntu5.3_amd64.deb \
    && dpkg -i libtinfo5_6.1-1ubuntu1_amd64.deb \
    && dpkg -i libtinfo-dev_6.1-1ubuntu1_amd64.deb \
    && dpkg -i readline-common_7.0-3_all.deb \
    && dpkg -i libreadline7_7.0-3_amd64.deb \
    && dpkg -i libreadline-dev_7.0-3_amd64.deb \
    && dpkg -i libsqlite3-0_3.22.0-1ubuntu0.3_amd64.deb \
    && dpkg -i sqlite3_3.22.0-1ubuntu0.3_amd64.deb \
    && rm -f * \
    && apt-get update \
    && apt-get upgrade -y \
    && rm -rf /var/lib/apt/lists/*

COPY --from=build /usr/local/rvm /usr/local/rvm
COPY --from=build /etc/profile.d/rvm.sh /etc/profile.d/
