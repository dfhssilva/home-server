FROM ruby:3.0

# throw errors if Gemfile has been modified since Gemfile.lock
# comment when developing to allow gem installation
RUN bundle config --global frozen 1

WORKDIR /usr/src/app/myblog

COPY ./myblog/Gemfile ./myblog/Gemfile.lock ./

# Install the jekyll and bundler gems
RUN gem install jekyll bundler && bundle install

EXPOSE 4000

# Build the site and make it available on a local server
CMD bundle exec jekyll serve --livereload --host 0.0.0.0
