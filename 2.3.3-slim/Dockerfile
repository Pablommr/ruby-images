FROM debian:9.13-slim

CMD ["/bin/bash"]

RUN apt-get update && \
    apt-get install -y --no-install-recommends bzip2 ca-certificates libffi-dev libgdbm3 libssl1.0-dev libyaml-dev libyaml-dev procps zlib1g-dev && \
    rm -rf /var/lib/apt/lists/* && \
    mkdir -p /usr/local/etc && \
    { echo 'install: --no-document'; echo 'update: --no-document'; } >> /usr/local/etc/gemrc

ENV RUBY_MAJOR=2.3
ENV RUBY_VERSION=2.3.3
ENV RUBY_DOWNLOAD_SHA256=1a4fa8c2885734ba37b97ffdb4a19b8fba0e8982606db02d936e65bac07419dc
ENV RUBYGEMS_VERSION=2.6.11

RUN set -ex && \
    buildDeps='autoconf bison gcc libbz2-dev libgdbm-dev libglib2.0-dev libncurses-dev libreadline-dev libxml2-dev libxslt-dev make ruby wget xz-utils' && \
    apt-get update  && \
    apt-get install -y --no-install-recommends $buildDeps && \
    rm -rf /var/lib/apt/lists/* && \
    wget -O ruby.tar.xz "https://cache.ruby-lang.org/pub/ruby/${RUBY_MAJOR%-rc}/ruby-$RUBY_VERSION.tar.xz" && \
    echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.xz" | sha256sum -c - && \
    mkdir -p /usr/src/ruby && \
    tar -xJf ruby.tar.xz -C /usr/src/ruby --strip-components=1 && \
    rm ruby.tar.xz && \
    cd /usr/src/ruby && \
    { echo '#define ENABLE_PATH_CHECK 0'; echo; cat file.c; } > file.c.new && \
    mv file.c.new file.c && \
    autoconf && \
    ./configure --disable-install-doc --enable-shared && \
    make -j"$(nproc)" && \
    make install && \
    apt-get purge -y --auto-remove $buildDeps && \
    cd / && rm -r /usr/src/ruby && \
    gem update --system "$RUBYGEMS_VERSION"

ENV BUNDLER_VERSION=1.14.6

RUN gem install bundler -v "$BUNDLER_VERSION"

ENV GEM_HOME=/usr/local/bundle
ENV BUNDLE_PATH=/usr/local/bundle
ENV BUNDLE_BIN=/usr/local/bundle/bin
ENV BUNDLE_SILENCE_ROOT_WARNING=1
ENV BUNDLE_APP_CONFIG=/usr/local/bundle
ENV PATH=/usr/local/bundle/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

RUN mkdir -p "$GEM_HOME"

CMD ["irb"]