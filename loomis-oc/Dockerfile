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

COPY run.sh               /run.sh

COPY openclinica.zip /tmp/openclinica.zip
COPY openclinica-ws.zip /tmp/openclinica-ws.zip

RUN unzip -o /tmp/openclinica.zip -d /tmp
RUN unzip -o /tmp/openclinica-ws.zip -d /tmp

#### Remove default webapps
RUN rm -rf $CATALINA_HOME/webapps/*

#### Create OpenClinica Home and Install
RUN mkdir $OC_HOME 
RUN cd $OC_HOME
RUN cp /tmp/OpenClinica-$OC_VERSION/distribution/OpenClinica.war .
RUN unzip -o OpenClinica.war -d ${OC_HOME}
RUN cd ..

#### Create OpenClinica Webapps and Install
RUN mkdir $OC_WS_HOME
RUN cd $OC_WS_HOME
RUN cp /tmp/OpenClinica-ws-$OC_VERSION/distribution/OpenClinica-ws.war .
RUN unzip -o OpenClinica-ws.war -d ${OC_WS_HOME}
RUN cd ..

RUN rm -rf /tmp/*

RUN mkdir $CATALINA_HOME/openclinica.data/xslt -p
RUN chmod +x /*.sh

ENV  JAVA_OPTS -Xmx1280m -XX:+UseParallelGC -XX:+CMSClassUnloadingEnabled

CMD ["/run.sh"]

