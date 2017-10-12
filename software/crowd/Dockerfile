FROM hub.slyak.com/common

RUN groupadd -r atlassian && useradd -r -g atlassian atlassian

# Setup useful environment variables
ENV CROWD_HOME     /var/atlassian/crowd
ENV CROWD_INSTALL  /opt/atlassian/crowd
ENV CROWD_WEB      ${CROWD_INSTALL}/crowd-webapp/WEB-INF
ENV CROWD_WEB_LIB  ${CROWD_WEB}/lib
ENV CROWD_VERSION  3.0.1

# Install Atlassian crowd and hepler tools and setup initial home
# directory structure.
ADD atlassian-crowd-${CROWD_VERSION}.tar /
RUN mkdir -p /opt/atlassian \
    && mv /atlassian-crowd-${CROWD_VERSION} ${CROWD_INSTALL}

RUN rm -f ${CROWD_WEB_LIB}/atlassian-extras-3.2.jar

#conf crack
COPY atlassian-extras-3.2.jar ${CROWD_WEB_LIB}/

#mysql
COPY mysql-connector-java-5.1.39.jar ${CROWD_INSTALL}/apache-tomcat/lib/

RUN mkdir -p "${CROWD_HOME}" \
  && echo "\ncrowd.home=$CROWD_HOME" >> "${CROWD_WEB}/classes/crowd-init.properties" \
  && chown -R atlassian "${CROWD_INSTALL}" \
  && chown -R atlassian "${CROWD_HOME}"


VOLUME ["/var/atlassian/crowd"]

EXPOSE 8095
ENTRYPOINT ["/opt/atlassian/crowd/apache-tomcat/bin/catalina.sh", "run"]