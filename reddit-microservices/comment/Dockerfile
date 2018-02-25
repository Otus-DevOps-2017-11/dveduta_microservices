FROM ruby:2.2

ENV COMMENT_DATABASE_HOST comment_db
ENV COMMENT_DATABASE comments
ENV APP_HOME /app

COPY . $APP_HOME

WORKDIR $APP_HOME

RUN apt-get update -qq \
 && apt-get install -y --no-install-recommends build-essential \
 && bundle install \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

CMD ["puma"]
