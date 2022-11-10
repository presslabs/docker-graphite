FROM python:3.8
ENV WORKERS=9
ENV DJANGO_SETTINGS_MODULE=graphite.settings
ENV GRAPHITE_VERSION=1.1.10

RUN set -ex \
    # https://github.com/graphite-project/graphite-web/issues/2640
    && pip install pip==20.1 \

    && pip install gunicorn==20.1.0  django-environ==0.9.0 whitenoise==4.1.4 django-auth-oidc==0.6.0 \
    && pip install graphite-web==${GRAPHITE_VERSION} whisper==${GRAPHITE_VERSION} \
        --install-option="--install-scripts=/usr/local/bin" \
        --install-option="--install-lib=/usr/local/lib/python3.8/site-packages" \
        --install-option="--install-data=/opt/graphite" \
    && echo "if settings.AUTH_SERVER:" >> /usr/local/lib/python3.8/site-packages/graphite/urls.py \
    && echo "    urlpatterns += [url(r'^auth/', include('django_auth_oidc.urls'))]" >> /usr/local/lib/python3.8/site-packages/graphite/urls.py \
    && addgroup --system --gid 105 graphite \
    && adduser --system --uid 105 --gid 105 --home /opt/graphite --no-create-home --shell /bin/false --gecos "Graphite User" graphite \
    && chown -R graphite:graphite /opt/graphite

COPY local_settings.py /usr/local/lib/python3.8/site-packages/graphite/local_settings.py

USER graphite
WORKDIR /opt/graphite
RUN set -ex \
    && SECRET_KEY="bootstrap" django-admin migrate --run-syncdb \
    && SECRET_KEY="bootstrap" django-admin collectstatic --no-input

EXPOSE 8080
CMD gunicorn graphite.wsgi -b0.0.0.0:8080 -w${WORKERS} --log-level INFO
