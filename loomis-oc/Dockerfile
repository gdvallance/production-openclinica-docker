# Forked from  Jens Piegsa (piegsa@gmail.com)

# Dockerfile for OpenClinica
#
# - for testing purposes only
# - needs an additional postgres container

FROM tomcat:7.0-jre8

ENV  OC_HOME              $CATALINA_HOME/webapps/OpenClinica
ENV  OC_WS_HOME           $CATALINA_HOME/webapps/OpenClinica-ws

ENV  OC_VERSION           3.14

COPY run.sh               /run.sh

RUN  mkdir /tmp/oc
RUN  cd /tmp/oc

COPY openclinica.zip /tmp/oc/openclinica.zip
COPY openclinica-ws.zip /tmp/oc/openclinica-ws.zip

RUN unzip -o /tmp/oc/openclinica.zip -d /tmp/oc
RUN unzip -o /tmp/oc/openclinica-ws.zip -d /tmp/oc

#### Remove default webapps
RUN rm -rf $CATALINA_HOME/webapps/*

#### Create OpenClinica Home and Install
RUN mkdir $OC_HOME 
RUN cd $OC_HOME
RUN cp /tmp/oc/OpenClinica-$OC_VERSION/distribution/OpenClinica.war .
RUN unzip -o OpenClinica.war -d ${OC_HOME}
RUN cd ..

#### Create OpenClinica Webapps and Install
RUN mkdir $OC_WS_HOME
RUN cd $OC_WS_HOME
RUN cp /tmp/oc/OpenClinica-ws-$OC_VERSION/distribution/OpenClinica-ws.war .
RUN unzip -o OpenClinica-ws.war -d ${OC_WS_HOME}
RUN cd ..

RUN rm -rf /tmp/oc

RUN mkdir $CATALINA_HOME/openclinica.data/xslt -p
# RUN mv $CATALINA_HOME/webapps/OpenClinica/WEB-INF/lib/servlet-api-2.3.jar ../ 
RUN chmod +x /*.sh

ENV  JAVA_OPTS -Xmx1280m -XX:+UseParallelGC -XX:+CMSClassUnloadingEnabled

RUN ls -al $OC_HOME
RUN mv /usr/local/tomcat/webapps/OpenClinica /usr/local/tomcat/webapps/loomis
RUN ls -al /usr/local/tomcat/webapps/loomis

CMD ["/run.sh"]

