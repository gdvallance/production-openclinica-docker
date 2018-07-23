# Dockerfile for OpenClinica
#
# - for testing purposes only
# - needs an additional postgres container

FROM tomcat:7.0-jre8

MAINTAINER Forked from  Jens Piegsa (piegsa@gmail.com)

ENV  OC_HOME              $CATALINA_HOME/webapps/OpenClinica
ENV  OC_WS_HOME           $CATALINA_HOME/webapps/OpenClinica-ws

ENV  OC_VERSION           3.14

COPY run.sh               /run.sh

RUN  mkdir /tmp/oc
RUN  cd /tmp/oc

COPY openclinica.zip /tmp/oc/openclinica.zip
COPY openclinica-ws.zip /tmp/oc/openclinica-ws.zip

RUN unzip /tmp/oc/openclinica.zip -d /tmp/oc
RUN unzip /tmp/oc/openclinica-ws.zip -d /tmp/oc

#### Remove default webapps
RUN rm -rf $CATALINA_HOME/webapps/*
RUN mkdir $OC_HOME 
RUN cd $OC_HOME
RUN cp /tmp/oc/OpenClinica-$OC_VERSION/distribution/OpenClinica.war .
RUN unzip OpenClinica.war
RUN cd ..
    
RUN mkdir $OC_WS_HOME
RUN cd $OC_WS_HOME
RUN cp /tmp/oc/OpenClinica-ws-$OC_VERSION/distribution/OpenClinica-ws.war .
RUN unzip OpenClinica-ws.war
RUN cd ..

RUN rm -rf /tmp/oc

RUN mkdir $CATALINA_HOME/openclinica.data/xslt -p
RUN mv $CATALINA_HOME/webapps/OpenClinica/WEB-INF/lib/servlet-api-2.3.jar ../ 
RUN chmod +x /*.sh

ENV  JAVA_OPTS -Xmx1280m -XX:+UseParallelGC -XX:+CMSClassUnloadingEnabled

RUN mv /usr/local/tomcat/webapps/OpenClinica /usr/local/tomcat/webapps/loomis

CMD ["/run.sh"]

