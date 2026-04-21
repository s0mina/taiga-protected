# This Source Code Form is subject to the terms of the Mozilla Public
# License, v. 2.0. If a copy of the MPL was not distributed with this
# file, You can obtain one at http://mozilla.org/MPL/2.0/.
#
# Copyright (c) 2021-present Kaleidos INC

FROM python:3.11-slim
LABEL maintainer="support@taiga.io"

ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONFAULTHANDLER=1

RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

COPY . /taiga-protected
WORKDIR /taiga-protected

RUN set -eux; \
    apt-get update; \
    apt-get install -y \
       build-essential \
       git \
       ca-certificates \
       wget; \
    # install su-exec (replaces gosu, works on all architectures)
    wget -O /usr/local/bin/su-exec "https://github.com/ncopa/su-exec/releases/download/v0.2/su-exec"; \
    chmod +x /usr/local/bin/su-exec; \
    # install Taiga protected dependencies
    python -m pip install --upgrade pip; \
    python -m pip install wheel; \
    python -m pip install -r requirements.txt; \
    chmod +x docker/entrypoint.sh; \
    # create taiga group and user
    groupadd --system taiga --gid=999; \
    useradd --system --no-create-home --gid taiga --uid=999 --shell=/bin/bash taiga; \
    chown -R taiga:taiga /taiga-protected; \
    # cleanup
    apt-get purge -y \
       build-essential \
       git \
       wget; \
    apt-get autoremove -y; \
    rm -rf /var/lib/apt/lists/*; \
    rm -rf /root/.cache; \
    rm requirements.txt; \
    find . -name '__pycache__' -exec rm -r '{}' +; \
    find . -name '*pyc' -exec rm -r '{}' +

EXPOSE 8003
ENTRYPOINT ["./docker/entrypoint.sh"]
