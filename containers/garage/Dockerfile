FROM centos:8
MAINTAINER tsukasa.oishi@gmail.com

ARG USER
ARG RUBY_VER
ARG NODE_VER
ARG PYTHON_VER
ARG GO_VER
ARG GIT_USER
ARG GIT_MAILADDRESS

# Add mysql & postgres repo
RUN dnf localinstall -y \
  http://dev.mysql.com/get/mysql80-community-release-el8-1.noarch.rpm \
  https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm \
  && dnf -y module disable postgresql

# install packages
RUN dnf -y update && dnf install -y \
  epel-release \
  dnf-plugins-core \
  && dnf config-manager --set-enabled PowerTools \
  && dnf clean all

RUN dnf -y update && dnf install -y \
  autoconf \
  automake \
  bind-utils \
  bison \
  bzip2 \
  curl-devel \
  epel-release \
  fftw3 \
  gcc-c++ \
  gdbm-devel \
  git \
  glibc-headers \
  ImageMagick \
  ImageMagick-devel \
  langpacks-ja \
  libffi-devel \
  libpng-devel \
  libtool \
  libtool-ltdl \
  libXt \
  libyaml-devel \
  make \
  mysql-devel \
  openssl-devel \
  patch \
  postgresql13-devel \
  procps \
  readline \
  readline-devel \
  screen \
  sudo \
  tar \
  util-linux-user \
  vim \
  wget \
  libyaml-devel \
  zlib \
  zlib-devel \
  zsh \
  && dnf clean all

# install git lfs
RUN curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.rpm.sh | sudo bash
RUN yum install -y git-lfs && yum clean all

# install gcloud & kubectl
COPY google-cloud-sdk.repo /etc/yum.repos.d/google-cloud-sdk.repo
RUN yum install -y google-cloud-sdk kubectl && yum clean all

# Create User
RUN useradd -m ${USER}
RUN echo "${USER}:${USER}" | chpasswd
RUN echo "${USER} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN chsh ${USER} -s /bin/zsh

ENV LANG="ja_JP.UTF-8" LANGUAGE="ja_JP:ja" LC_ALL="ja_JP.UTF-8"

# sqlite3
WORKDIR /tmp
RUN wget https://www.sqlite.org/2020/sqlite-autoconf-3310100.tar.gz \
    && tar xfz sqlite-autoconf-3310100.tar.gz
WORKDIR /tmp/sqlite-autoconf-3310100
RUN ./configure && make && make install

# switch user
USER ${USER}
WORKDIR /home/${USER}
RUN git lfs install

# install oh-my-zsh
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
SHELL ["/bin/zsh", "-c"]
COPY zshrc .zshrc

# install vim-plug
RUN curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
COPY vimrc .vimrc
RUN vim -c PlugInstall -c q -c q

# install rbenv
RUN git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
RUN git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

# install nodenv
RUN git clone https://github.com/nodenv/nodenv.git ~/.nodenv
WORKDIR /home/${USER}/.nodenv
RUN src/configure && make -C src
RUN mkdir -p ~/.nodenv/plugins
RUN git clone https://github.com/nodenv/node-build.git ~/.nodenv/plugins/node-build
RUN git clone https://github.com/pine/nodenv-yarn-install.git ~/.nodenv/plugins/nodenv-yarn-install
WORKDIR /home/${USER}

# install pyenv
RUN git clone git://github.com/yyuu/pyenv.git ~/.pyenv

# install goenv
RUN git clone https://github.com/syndbg/goenv.git ~/.goenv

# install ruby & node & python & golang
RUN source .zshrc; rbenv install ${RUBY_VER}; rbenv global ${RUBY_VER}
RUN source .zshrc; nodenv install ${NODE_VER}; nodenv global ${NODE_VER}
RUN source .zshrc; pyenv install ${PYTHON_VER}; pyenv global ${PYTHON_VER}
RUN source .zshrc; goenv install ${GO_VER}; goenv global ${GO_VER}

# install aws cli
RUN source .zshrc; pip install awscli

# git config
RUN git config --global user.name "${GIT_USER}"
RUN git config --global user.email "${GIT_MAILADDRESS}"
RUN git config --global color.ui true
RUN git config --global alias.stat status
RUN git config --global core.editor vim


# prepare workspace
RUN mkdir develop
