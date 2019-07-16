FROM php

RUN apt-get update && apt-get install --no-install-recommends -y \
  git vim \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/

ENV PATH="${PATH}:/arc/arcanist/bind/"

# Set shell to bash
ENV SHELL /bin/bash

# Set locale
ENV LANG "C.UTF-8"
ENV LC_COLLATE "C.UTF-8"

ENTRYPOINT ["/arc/arcanist/bin/arc"]
