FROM python:3.7
ENV WORKERS=9
ENV DJANGO_SETTINGS_MODULE=graphite.settings
ENV GRAPHITE_VERSION=1.1.5

RUN set -ex \
    && pip install gunicorn==19.9.0 django-environ==0.4.5 whitenoise==4.1.2 django-auth-oidc==0.5.0 'Django<2.1' \
    && pip install graphite-web==${GRAPHITE_VERSION} whisper==${GRAPHITE_VERSION} \
        --install-option="--install-scripts=/usr/local/bin" \
        --install-option="--install-lib=/usr/local/lib/python3.7/site-packages" \
        --install-option="--install-data=/opt/graphite" \
    && echo "if settings.AUTH_SERVER:" >> /usr/local/lib/python3.7/site-packages/graphite/urls.py \
    && echo "    urlpatterns += [url(r'^auth/', include('django_auth_oidc.urls'))]" >> /usr/local/lib/python3.7/site-packages/graphite/urls.py \
    && addgroup --system --gid 105 graphite \
    && adduser --system --uid 105 --gid 105 --home /opt/graphite --no-create-home --shell /bin/false --gecos "Graphite User" graphite \
    && chown -R graphite:graphite /opt/graphite

COPY local_settings.py /usr/local/lib/python3.7/site-packages/graphite/local_settings.py

USER graphite
WORKDIR /opt/graphite
RUN set -ex \
    && SECRET_KEY="bootstrap" django-admin migrate --run-syncdb \
    && SECRET_KEY="bootstrap" django-admin collectstatic --no-input

EXPOSE 8080
CMD gunicorn graphite.wsgi -b0.0.0.0:8080 -w${WORKERS} --log-level INFO
