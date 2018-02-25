FROM ubuntu:16.04

ENV COMMENT_SERVICE_HOST comment
ENV COMMENT_SERVICE_PORT 9292
ENV APP_HOME /app
ENV POST_SERVICE_HOST post
ENV POST_SERVICE_PORT 5000

COPY . $APP_HOME

WORKDIR $APP_HOME

RUN apt-get update -qq \
 && apt-get install -y --no-install-recommends ruby-full build-essential \
 && gem install bundler --no-ri --no-rdoc \
 && bundle install \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

CMD ["puma"]
