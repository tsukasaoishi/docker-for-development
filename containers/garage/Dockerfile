FROM centos:7
MAINTAINER tsukasa.oishi@gmail.com
ARG USER
ARG RUBY_VER

RUN yum -y update && yum install -y \
  epel-release \
  autoconf \
  automake \
  bison \
  bzip2 \
  curl-devel \
  fftw3 \
  gcc-c++ \
  gdbm-devel \
  git \
  glibc-headers \
  ImageMagick \
  ImageMagick-devel \
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
  procps \
  readline \
  readline-devel \
  screen \
  sqlite-devel \
  sudo \
  tar \
  vim \
  wget \
  wgetlibyaml-devel \
  zlib \
  zlib-devel \
  zsh \
  && yum clean all

# install git lfs
RUN curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.rpm.sh | sudo bash
RUN yum install -y git-lfs && yum clean all

# install imagemagick
#RUN yum install -y \
#  https://www.imagemagick.org/download/linux/CentOS/x86_64/ImageMagick-libs-7.0.8-26.x86_64.rpm \
#  https://www.imagemagick.org/download/linux/CentOS/x86_64/ImageMagick-7.0.8-26.x86_64.rpm \
#  && yum clean all

# Create User
RUN useradd -m ${USER}
RUN echo "${USER}:${USER}" | chpasswd
RUN echo "${USER} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN chsh ${USER} -s /bin/zsh

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
RUN git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-buildv
RUN source .zshrc; rbenv install ${RUBY_VER}; rbenv global ${RUBY_VER}; gem install bundler

# git config
RUN git config --global user.name "Tsukasa OISHI"
RUN git config --global user.email "tsukasa.oishi@gmail.com"

# prepare workspace
RUN mkdir develop

