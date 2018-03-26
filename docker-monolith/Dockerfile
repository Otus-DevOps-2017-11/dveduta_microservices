FROM ubuntu:16.04

RUN apt-get update \
 && apt-get install -y mongodb-server ruby-full ruby-dev build-essential git \
 && gem install bundler \
 && git clone https://github.com/Artemmkin/reddit.git

COPY mongod.conf /etc/mongod.conf
COPY db_config /reddit/db_config
COPY start.sh /start.sh

RUN cd /reddit && bundle install && chmod a+x /start.sh

CMD ["/start.sh"]