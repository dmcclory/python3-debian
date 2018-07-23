FROM python:3.6

# Allows you to add additional packages via build-arg
ARG ADDITIONAL_PACKAGE

RUN echo "Pulling watchdog binary from Github." \
    && curl -sSL https://github.com/openfaas/faas/releases/download/0.8.0/fwatchdog > /usr/bin/fwatchdog \
    && chmod +x /usr/bin/fwatchdog

# Add non root user
RUN adduser --system app
RUN addgroup --system app
RUN adduser app app

WORKDIR /home/app/

COPY index.py           .
COPY requirements.txt   .

RUN chown -R app /home/app

USER app
ENV PATH=$PATH:/home/app/.local/bin
RUN pip install --user --upgrade pip
RUN pip install --user -r requirements.txt

RUN mkdir -p function
RUN touch ./function/__init__.py

WORKDIR /home/app/function/
COPY function/requirements.txt	.
RUN pip install --user -r requirements.txt

WORKDIR /home/app/

USER root
COPY function           function
RUN chown -R app:app ./
USER app

ENV fprocess="python3 index.py"

HEALTHCHECK --interval=1s CMD [ -e /tmp/.lock ] || exit 1

CMD ["fwatchdog"]
