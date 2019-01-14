FROM centos:centos7

# Import the Centos-7 RPM GPG key to prevent warnings
RUN rpm --import http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-7
RUN rpm --import http://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-7

# system update
RUN yum -y update && yum clean all

# set locale
RUN yum reinstall -y glibc-common && yum clean all
RUN localedef -f UTF-8 -i ja_JP ja_JP.UTF-8
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:ja
ENV LC_ALL ja_JP.UTF-8
RUN unlink /etc/localtime
RUN ln -s /usr/share/zoneinfo/Japan /etc/localtime

# base packages
RUN yum --enablerepo=extras clean metadata
RUN yum install -y zlib zlib-devel make gcc gcc-c++ git vim
# RUN yum install -y openssl openssl-devel readline-devel pcre pcre-devel
# RUN yum install -y tar zip unzip bzip2 which iproute mlocate
RUN yum install -y zsh tree wget

# install geth ###############################################
# install golang
RUN yum -y install epel-release
RUN yum -y install golang gmp-devel
# install geth
WORKDIR /root
ENV GETH_VERSION v1.8.20
RUN git clone --branch $GETH_VERSION https://github.com/ethereum/go-ethereum.git --depth 1
WORKDIR go-ethereum
RUN make geth \
    && cp build/bin/geth /usr/local/bin

# install solidity ###########################################
# gcc 5.3
RUN yum install -y centos-release-scl centos-release-scl-rh
RUN yum install -y devtoolset-4-gcc devtoolset-4-gcc-c++ devtoolset
RUN scl enable devtoolset-4 bash

# cmake 3.7.0
RUN yum -y install zlib-devel libcurl-devel
RUN mkdir /root/cmake
WORKDIR /root/cmake
RUN wget https://cmake.org/files/v3.7/cmake-3.7.0.tar.gz
RUN tar xzvf cmake-3.7.0.tar.gz
WORKDIR /root/cmake/cmake-3.7.0
RUN ./configure --system-curl
RUN make && make install

# boost
RUN yum -y install python-devel
RUN mkdir /root/boost
WORKDIR /root/boost
RUN wget https://sourceforge.net/projects/boost/files/boost/1.62.0/boost_1_62_0.tar.gz
RUN tar xzvf boost_1_62_0.tar.gz
WORKDIR /root/boost/boost_1_62_0
RUN ./bootstrap.sh && ./b2 install
RUN echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
RUN /sbin/ldconfig
ENV BOOST_INCLUDEDIR /usr/local/include
ENV BOOST_LIBRARYDIR /usr/local/lib
RUN echo "export BOOST_INCLUDEDIR=/usr/local/include" >> /etc/profile
RUN echo "export BOOST_LIBRARYDIR=/usr/local/lib" >> /etc/profile

# jsoncpp
# WORKDIR /root
# RUN git clone https://github.com/open-source-parsers/jsoncpp.git
# RUN mkdir -p /root/jsoncpp/build/release
# WORKDIR /root/jsoncpp/build/release
# RUN cmake -DCMAKE_BUILD_TYPE=release -DBUILD_STATIC_LIBS=ON -DBUILD_SHARED_LIBS=ON -DARCHIVE_INSTALL_DIR=. -G "Unix Makefiles" ../..
# RUN make && make install
RUN yum -y install jsoncpp jsoncpp-devel

# z3 (z3-4.8.4)
WORKDIR /root
RUN git clone https://github.com/Z3Prover/z3.git
WORKDIR /root/z3
RUN git checkout z3-4.8.4
WORKDIR /root/z3
RUN python scripts/mk_make.py
RUN cd build/ && make && make install

# solidity
WORKDIR /root
RUN git clone --recursive https://github.com/ethereum/solidity.git
WORKDIR /root/solidity
RUN git checkout release
RUN mkdir build
WORKDIR /root/solidity/build
# use g++ 5.3.1
RUN cmake -DCMAKE_CXX_COMPILER=/opt/rh/devtoolset-4/root/usr/bin/g++ ..
RUN make && make install
RUN /sbin/ldconfig
