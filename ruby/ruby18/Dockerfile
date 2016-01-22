FROM buildpack-deps:trusty

ENV RUBY_MAJOR 1.8
ENV RUBY_VERSION 1.8.7-p374
ENV RUBY_DOWNLOAD_SHA256 df795f2f99860745a416092a4004b016ccf77e8b82dec956b120f18bdc71edce
ENV RUBYGEMS_VERSION 2.4.8

RUN echo 'install: --no-document\nupdate: --no-document' >> "$HOME/.gemrc"

# https://cache.ruby-lang.org/pub/ruby/ruby-1.8.7-p358.tar.gz

# we need bison v2.3 because gem ripper (for ripper-tags) does not install with newer versions
RUN curl -fSL -o bison.tar.gz 'http://ftp.gnu.org/gnu/bison/bison-2.3.tar.gz' \
  && mkdir -p /usr/src/bison \
  && tar -xvf bison.tar.gz -C /usr/src/bison --strip-components=1 \
  && rm bison.tar.gz \
  && cd /usr/src/bison \
  && ./configure --disable-install-doc \
  && make -j"$(nproc)" \
  && make install

# some of ruby's build scripts are written in ruby
# we purge this later to make sure our final image uses what we just built
RUN apt-get update \
  && apt-get install -y libgdbm-dev ruby \
  && rm -rf /var/lib/apt/lists/* \
  && mkdir -p /usr/src/ruby \
  && curl -fSL -o ruby.tar.gz "http://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.gz" \
  && tar -xzf ruby.tar.gz -C /usr/src/ruby --strip-components=1 \
  && rm ruby.tar.gz \
  && cd /usr/src/ruby \
  && autoconf \
  && ./configure --disable-install-doc \
  && make -j"$(nproc)" \
  && make install

RUN curl -fSL -o rubygems-2.4.8.tgz "https://rubygems.org/rubygems/rubygems-2.4.8.tgz" \
  && mkdir -p /usr/src/rubygems \
  && tar -xzf rubygems-2.4.8.tgz -C /usr/src/rubygems --strip-components=1 \
  && cd /usr/src/rubygems \
  && ruby setup.rb \
  && rm /rubygems-2.4.8.tgz && rm -rf /usr/src/rubygems

# install things globally, for great justice
ENV GEM_HOME /usr/local/bundle
ENV PATH $GEM_HOME/bin:$PATH
ENV BUNDLER_VERSION 1.6.5

RUN gem install bundler --version "$BUNDLER_VERSION" \
  && bundle config --global path "$GEM_HOME" \
  && bundle config --global bin "$GEM_HOME/bin"

# don't create ".bundle" in all our apps
ENV BUNDLE_APP_CONFIG $GEM_HOME

CMD [ "irb" ]
