FROM slyak/common
ENV  FISHEYE_HOME /var/atlassian/fisheye
ENV  FISHEYE_INST /opt/atlassian/fisheye
ENV  FISHEYE_VERSION 4.4.2

ENV UID fisheye
ENV GID atlassian
ENV LANG C.UTF-8

# TODO The Universal Plugin Manager (v2.21.5)

ADD fecru-$FISHEYE_VERSION.zip /
RUN mkdir -p /opt/atlassian \
    && unzip -q fecru-$FISHEYE_VERSION.zip && mv fecru-$FISHEYE_VERSION ${FISHEYE_INST}

#crack
COPY atlassian-extras-2.5.jar ${FISHEYE_INST}/lib/

#mysql
COPY mysql-connector-java-5.1.39.jar ${FISHEYE_INST}/lib/


RUN groupadd -r $GID \
    && useradd -r --create-home --home-dir $FISHEYE_INST --groups $GID --shell /bin/bash $UID

EXPOSE 8060

WORKDIR $FISHEYE_INST
VOLUME ["${FISHEYE_INST}/var","${FISHEYE_HOME}"]
ENTRYPOINT ["/opt/atlassian/fisheye/bin/fisheyectl.sh","run"]