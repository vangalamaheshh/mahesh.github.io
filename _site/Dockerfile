FROM jekyll/jekyll:latest

ARG JEKYLL_DIR /srv/jekyll

WORKDIR ${JEKYLL_DIR}

RUN set -ex \
  && gem install jekyll bundler

