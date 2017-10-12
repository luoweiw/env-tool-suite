FROM hub.slyak.com/common
# Configuration variables.
ENV JIRA_HOME     /var/atlassian/jira
ENV JIRA_INSTALL  /opt/atlassian/jira
ENV JIRA_WEB      ${JIRA_INSTALL}/atlassian-jira/WEB-INF
ENV JIRA_WEB_LIB  ${JIRA_WEB}/lib
ENV JIRA_VERSION  7.3.6

#application.login.url                   http://crowd:8095/crowd/console/
#crowd.server.url                        http://crowd:8095/crowd/services/
#crowd.base.url                          http://crowd:8095/crowd/

# Install Atlassian JIRA and helper tools and setup initial home
# directory structure.

ADD atlassian-jira-software-${JIRA_VERSION}.tar /
RUN mkdir -p /opt/atlassian \
    && mv /atlassian-jira-software-${JIRA_VERSION}-standalone ${JIRA_INSTALL}

#use mysql
COPY mysql-connector-java-5.1.39.jar ${JIRA_WEB_LIB}/

#jira crack
COPY atlassian-extras-3.2.jar ${JIRA_WEB_LIB}/

#crowd sso
COPY crowd/crowd-integration-client-3.0.1.jar ${JIRA_WEB_LIB}/
COPY crowd/conf/crowd.properties  ${JIRA_WEB}/classes/
COPY crowd/conf/crowd-ehcache.xml ${JIRA_WEB}/classes/
COPY seraph-config.xml ${JIRA_WEB}/classes/
ENV CROWD_BASE_URL http://dev.slyak.com/crowd

RUN  rm "${JIRA_WEB}/atlassian-bundled-plugins/atlassian-universal-plugin-manager-plugin-2.21.jar"
COPY atlassian-universal-plugin-manager-plugin-2.22.jar ${JIRA_WEB}/atlassian-bundled-plugins/

RUN mkdir -p                   "${JIRA_HOME}" \
    && mkdir -p                "${JIRA_HOME}/caches/indexes" \
    && chmod -R 700            "${JIRA_HOME}" \
    && chown -R daemon:daemon  "${JIRA_HOME}" \
    && mkdir -p                "${JIRA_INSTALL}/conf/Catalina" \
#    && curl -Ls                "https://www.atlassian.com/software/jira/downloads/binary/atlassian-jira-core-${JIRA_VERSION}.tar.gz" | tar -xz --directory "${JIRA_INSTALL}" --strip-components=1 --no-same-owner \
#    && curl -Ls                "https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.38.tar.gz" | tar -xz --directory "${JIRA_INSTALL}/lib" --strip-components=1 --no-same-owner "mysql-connector-java-5.1.38/mysql-connector-java-5.1.38-bin.jar" \
#    && rm -f                   "${JIRA_INSTALL}/lib/postgresql-9.1-903.jdbc4-atlassian-hosted.jar" \
#    && curl -Ls                "https://jdbc.postgresql.org/download/postgresql-9.4.1212.jar" -o "${JIRA_INSTALL}/lib/postgresql-9.4.1212.jar" \
    && chmod -R 700            "${JIRA_INSTALL}/conf" \
    && chmod -R 700            "${JIRA_INSTALL}/logs" \
    && chmod -R 700            "${JIRA_INSTALL}/temp" \
    && chmod -R 700            "${JIRA_INSTALL}/work" \
    && chmod 700               "${JIRA_INSTALL}/bin/check-java.sh" \
    && chown -R daemon:daemon  "${JIRA_INSTALL}/conf" \
    && chown -R daemon:daemon  "${JIRA_INSTALL}/logs" \
    && chown -R daemon:daemon  "${JIRA_INSTALL}/temp" \
    && chown -R daemon:daemon  "${JIRA_INSTALL}/work" \
    && sed --in-place          "s/java version/openjdk version/g" "${JIRA_INSTALL}/bin/check-java.sh" \
    && echo -e                 "\njira.home=$JIRA_HOME" >> "${JIRA_INSTALL}/atlassian-jira/WEB-INF/classes/jira-application.properties" \
    && touch -d "@0"           "${JIRA_INSTALL}/conf/server.xml"



# Use the default unprivileged account. This could be considered bad practice
# on systems where multiple processes end up being executed by 'daemon' but
# here we only ever run one process anyway.
USER daemon:daemon

# Expose default HTTP connector port.
EXPOSE 8080

# Set volume mount points for installation and home directory. Changes to the
# home directory needs to be persisted as well as parts of the installation
# directory due to eg. logs.
VOLUME ["${JIRA_HOME}", "${JIRA_INSTALL}/logs"]

# Set the default working directory as the installation directory.
WORKDIR ${JIRA_INSTALL}

COPY "docker-entrypoint.sh" "/"
ENTRYPOINT ["/docker-entrypoint.sh"]

# Run Atlassian JIRA as a foreground process by default.
CMD ["/opt/atlassian/jira/bin/catalina.sh", "run"]