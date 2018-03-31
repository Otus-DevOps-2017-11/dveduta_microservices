FROM ubuntu:16.04

ENV COMMENT_SERVICE_HOST comment
ENV COMMENT_SERVICE_PORT 9292
ENV APP_HOME /app
ENV POST_SERVICE_HOST post
ENV POST_SERVICE_PORT 5000

# Обновление кеша версий пакетов и установка зависимостей, которые не зависят от обновления кода
# Очистка кеша версий пакетов для уменьшения размера слоя
RUN apt-get update -qq \
 && apt-get install -y --no-install-recommends ruby-full build-essential \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 && gem install bundler --no-ri --no-rdoc \

COPY . $APP_HOME

WORKDIR $APP_HOME

# Установка компонентов, зависящих от кода
RUN bundle install

CMD ["puma"]
