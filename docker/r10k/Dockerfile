FROM alpine:3.9 as build

RUN apk add --no-cache ruby git
RUN mkdir /workspace
WORKDIR /workspace
COPY . /workspace
RUN gem build r10k.gemspec && \
    mv r10k*.gem r10k.gem

FROM alpine:3.9

ARG vcs_ref
ARG build_date
ARG version="3.1.0"
# Used by entrypoint to submit metrics to Google Analytics.
# Published images should use "production" for this build_arg.
ARG pupperware_analytics_stream="dev"
ENV PUPPERWARE_ANALYTICS_STREAM="$pupperware_analytics_stream"

ENV R10K_VERSION="$version"

LABEL org.label-schema.maintainer="Puppet Release Team <release@puppet.com>" \
      org.label-schema.vendor="Puppet" \
      org.label-schema.url="https://github.com/puppetlabs/r10k" \
      org.label-schema.name="r10k" \
      org.label-schema.license="Apache-2.0" \
      org.label-schema.version="$R10K_VERSION" \
      org.label-schema.vcs-url="https://github.com/puppetlabs/r10k" \
      org.label-schema.vcs-ref="$vcs_ref" \
      org.label-schema.build-date="$build_date" \
      org.label-schema.schema-version="1.0" \
      org.label-schema.dockerfile="/Dockerfile"

RUN apk add --no-cache ruby openssh-client git ruby-rugged curl ruby-dev make gcc musl-dev
COPY --from=build /workspace/r10k.gem /
RUN gem install --no-doc r10k.gem json etc && \
    rm -f r10k.gem

COPY docker/r10k/docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh
COPY docker/r10k/docker-entrypoint.d /docker-entrypoint.d

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["help"]

COPY docker/r10k/Dockerfile /
