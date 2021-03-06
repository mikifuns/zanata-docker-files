FROM jboss/wildfly:10.1.0.Final
MAINTAINER "Ding-Yi Chen" <dchen@redhat.com>

EXPOSE 8080
# Version should be assigned in build time
# Take care editing the ZANATA_VERSION line: just version numbers,
# otherwise runapp.sh cannot parse it.
ARG ZANATA_VERSION=4.2.1
ARG MYSQL_CONNECTOR_JAVA_VERSION=5.1.26

# MAIL_USERNAME and MAIL_PASSWORD can not be empty as they are used in wildfly standalone-full.xml.
# If the smtp server does not require authentication, these single space values will be used
ENV DB_HOSTNAME=zanatadb MAIL_HOST=localhost MAIL_USERNAME=' ' MAIL_PASSWORD=' '

# Fedora Containers Packaging may need this
LABEL Name="zanata-platform"\
    Version=$ZANATA_VERSION\
    Release="1"

USER root

ENV ZANATA_HOME /var/lib/zanata
RUN mkdir -p $ZANATA_HOME && chown -R jboss.jboss $ZANATA_HOME
VOLUME $ZANATA_HOME

# Make the image Openshift compatible by granting root group (not user) access to the necessary directories
RUN chgrp -R 0 $ZANATA_HOME /opt/jboss/wildfly
RUN chmod -R g+rw $ZANATA_HOME /opt/jboss/wildfly
RUN find $ZANATA_HOME -type d -exec chmod g+x {} +
RUN find /opt/jboss/wildfly -type d -exec chmod g+x {} +

USER jboss

RUN curl -L -o /opt/jboss/wildfly/standalone/deployments/mysql-connector-java.jar https://repo1.maven.org/maven2/mysql/mysql-connector-java/${MYSQL_CONNECTOR_JAVA_VERSION}/mysql-connector-java-${MYSQL_CONNECTOR_JAVA_VERSION}.jar

# Zanata war
RUN curl -L -o /opt/jboss/wildfly/standalone/deployments/zanata.war \
    https://github.com/zanata/zanata-platform/releases/download/platform-${ZANATA_VERSION}/zanata-war-${ZANATA_VERSION}.war \
    && chmod o+rw /opt/jboss/wildfly/standalone/deployments/zanata.war

ADD conf/standalone.xml /opt/jboss/wildfly/standalone/configuration/
