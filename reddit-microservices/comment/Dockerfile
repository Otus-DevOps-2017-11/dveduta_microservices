FROM ruby:2.2

ENV COMMENT_DATABASE_HOST comment_db
ENV COMMENT_DATABASE comments
ENV APP_HOME /app

# Обновление кеша версий пакетов и установка зависимостей, которые не зависят от обновления кода
# Очистка кеша версий пакетов для уменьшения размера слоя
RUN apt-get update -qq \
 && apt-get install -y --no-install-recommends build-essential \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

COPY . $APP_HOME

WORKDIR $APP_HOME

# Установка компонентов, зависящих от кода
RUN bundle install

CMD ["puma"]
