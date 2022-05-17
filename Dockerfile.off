FROM ubuntu:focal

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y && apt-get upgrade -y && useradd -m actions

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    curl wget jq build-essential sudo libssl-dev libffi-dev python3 python3-venv python3-dev python3-pip

RUN \
    RUNNER_VERSION="$(curl -s -X GET 'https://api.github.com/repos/actions/runner/releases/latest' | jq -r '.tag_name|ltrimstr("v")')" \
    && cd /home/actions && mkdir actions-runner && cd actions-runner \
    && wget https://github.com/actions/runner/releases/download/v${RUNNER_VERSION}/actions-runner-linux-x64-${RUNNER_VERSION}.tar.gz \
    && tar xzf ./actions-runner-linux-x64-${RUNNER_VERSION}.tar.gz \
    && ./bin/installdependencies.sh \
    && chown -R actions ~actions

RUN add-apt-repository ppa:git-core/ppa -y \
    && apt-get update -y && apt-get install -y --no-install-recommends \
    build-essential git

RUN curl -sL https://raw.githubusercontent.com/mklement0/n-install/stable/bin/n-install | bash -s -- -ny - \
    && ~/n/bin/n lts \
    && npm install -g grunt gulp n parcel-bundler typescript newman \
    && npm install -g --save-dev webpack webpack-cli \
    && npm install -g npm \
    && rm -rf ~/n

WORKDIR /home/actions/actions-runner

USER actions
COPY --chown=actions:actions entrypoint.sh .
RUN chmod u+x ./entrypoint.sh
ENTRYPOINT ["./entrypoint.sh"]