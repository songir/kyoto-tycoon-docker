FROM centos:7
MAINTAINER Songir <songir@163.com>

RUN yum -y install epel-release && yum -y update \
    && yum -y install git libtool ruby ruby-devel ruby-libs rubygems xz gcc gcc-c++ automake zlib-devel openssl* make zlib-devel lzo-devel
RUN yum -y install lua-devel

ENV CFLAGS="-std=c++98"
ENV CXXFLAGS="-std=c++98"

RUN cd /tmp && \
    git clone https://github.com/songir/kyoto.git && \
    cd /tmp/kyoto && \
	make && \
	make install && \
	make clean && \
	cd && \
	rm -rf /tmp/kyoto
RUN mkdir -p /data/db

RUN rm -rf /tmp/build && git clone https://github.com/msgpack/msgpack-c.git /tmp/build \
    && cd /tmp/build \
    && git checkout -b cpp-0.5 origin/cpp-0.5 \
    && ./bootstrap \
    && ./configure \
    && make && make install \
    && rm -rf /tmp/build

RUN echo "Install mpio." && git clone https://github.com/jubatus/jubatus-mpio.git /tmp/build && cd /tmp/build \
    && git checkout -b 0.4.4 0.4.4 \
    && ./bootstrap \
    && ./configure \
    && make && make install \
    && rm -rf /tmp/build \
    && cd /usr/local/lib/ \
    && ln -s libjubatus_mpio.a libmpio.a \
    && ln -s libjubatus_mpio.la libmpio.la \
    && ln -s libjubatus_mpio.so.0.4.2 libmpio.so \
    && ln -s libjubatus_mpio.so.0.4.2 libmpio.so.0 \
    && echo "Symlink created for libmpio."

RUN git clone https://github.com/jubatus/jubatus-msgpack-rpc.git /tmp/build \
    && cd /tmp/build \
    && git checkout -b  0.4.4 \
    && cd /tmp/build/cpp \
    && ./bootstrap \
    && CPPFLAGS="-I/usr/local/include/ -I/usr/local/include/jubatus/ -I/usr/local/include/msgpack -L/usr/local/lib/ " ./configure \
    && make && make install \
    && rm -rf /tmp/build \
    && echo "Create symlink for libmsgpack-rpc." && cd /usr/local/lib/ \
   && ln -s libjubatus_msgpack-rpc.a libmsgpack-rpc.a  \
   && ln -s libjubatus_msgpack-rpc.la libmsgpack-rpc.la \
   && ln -s libjubatus_msgpack-rpc.so.0.4.2 libmsgpack-rpc.so \
   && ln -s libjubatus_msgpack-rpc.so.0.4.2 libmsgpack-rpc.so.0 \
   && echo "Symlink created for libmsgpack-rpc."

#RUN gem install mplex --no-document \
#    && gem install polyglot --no-document \
#    && gem install treetop --no-document \
#    && echo "Installed GEM packages for msgpack-rpc."

RUN yum -y remove ruby ruby-devel  ruby-libs rubygems
RUN yum install -y centos-release-scl
RUN yum install -y rh-ruby27 rh-ruby27-ruby-devel which \
    && scl enable rh-ruby27 bash

COPY rh-ruby.sh /etc/profile.d/rh-ruby.sh

RUN source scl_source enable rh-ruby27 && ruby -v

RUN source scl_source enable rh-ruby27 && gem install mplex --no-document \
    && gem install polyglot --no-document \
    && gem install treetop --no-document \
    && echo "Installed GEM packages for msgpack-rpc."

RUN echo "Install msgpack-idl..."  \
    && source scl_source enable rh-ruby27 \
    && git clone https://github.com/msgpack-rpc/msgpack-rpc.git /tmp/build  \
    && cd /tmp/build \
    && git checkout cpp-0.3.1 \
    && cd /tmp/build/idl/ \
    && ./bootstrap \
    && ./configure  \
    && make && make install \
    && cd /tmp \
    && rm -rf /tmp/build

RUN git clone https://github.com/frsyuki/kt-msgpack.git /tmp/build
RUN cd /tmp/build \
    && source scl_source enable rh-ruby27 \
    && ./bootstrap \
    && CPPFLAGS="-I/usr/local/include/ -I/usr/local/include/jubatus/ -L/usr/local/lib/ " ./configure --with-kc=/usr/local/  \
    && make && make install \
    && rm -rf /tmp/build

RUN rm -rf /usr/local/libexec/libktmsgpack.so \
    && rm -rf /usr/local/libexec/libktmsgpack.so.0 \
    && cp /usr/local/libexec/libktmsgpack.so.0.0.0 /usr/local/libexec/libktmsgpack.so \
    && cp /usr/local/libexec/libktmsgpack.so.0.0.0 /usr/local/libexec/libktmsgpack.so.0


RUN grep "/usr/local/lib" /etc/ld.so.conf || echo "/usr/local/lib" >> /etc/ld.so.conf && ldconfig

ENTRYPOINT ["ktserver"]

EXPOSE 1978