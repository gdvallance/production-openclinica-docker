# Forked from  Jens Piegsa (piegsa@gmail.com)

# Dockerfile for OpenClinica
#
# - for testing purposes only
# - needs an additional postgres container

FROM tomcat:7.0-jre8

ENV  OC_HOME              $CATALINA_HOME/webapps/OpenClinica
ENV  OC_WS_HOME           $CATALINA_HOME/webapps/OpenClinica-ws

ENV  OC_VERSION           3.14
ENV  DATABASE_NAME        default

ENV LOG_LEVEL             INFO
ENV TZ                    UTC-1
ENV DB_TYPE               postgres 
ENV DB_HOST               default
ENV DB_NAME               openclinica
ENV DB_USER               clinica
ENV DB_PASS               clinica 
ENV DB_PORT               5432
ENV SUPPORT_URL           "https://www.openclinica.com/community-edition-open-source-edc/"

COPY run.sh               /run.sh

COPY openclinica.zip /tmp/openclinica.zip
COPY openclinica-ws.zip /tmp/openclinica-ws.zip

RUN apt-get update && \
    apt-get install -y postgresql-client && \
    unzip -o /tmp/openclinica.zip -d /tmp && \
    unzip -o /tmp/openclinica-ws.zip -d /tmp && \ 
    rm -rf $CATALINA_HOME/webapps/* && \
    mkdir $OC_HOME && \
    cd $OC_HOME && \
    cp /tmp/OpenClinica-$OC_VERSION/distribution/OpenClinica.war . && \
    unzip -o OpenClinica.war -d ${OC_HOME} && \
    cd .. && \
    mkdir $OC_WS_HOME && \
    cd $OC_WS_HOME && \
    cp /tmp/OpenClinica-ws-$OC_VERSION/distribution/OpenClinica-ws.war . && \
    unzip -o OpenClinica-ws.war -d ${OC_WS_HOME} && \
    cd .. && \
    rm -rf /tmp/* && \
    mkdir $CATALINA_HOME/openclinica.data/xslt -p && \
    chmod +x /*.sh

ENV  JAVA_OPTS -Xmx1280m -XX:+UseParallelGC -XX:+CMSClassUnloadingEnabled

CMD ["/run.sh"]