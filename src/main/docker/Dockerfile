FROM java:8
MAINTAINER sandro.boehme@gmx.de
COPY ./sample-launchpad-${project.version}.jar /opt/sling/
WORKDIR /opt/sling/
EXPOSE 8080
VOLUME /opt/sling/sling
ENV JAVA_OPTS -Xmx512m
ENV SLING_OPTS ''
CMD exec java $JAVA_OPTS -jar sample-launchpad-${project.version}.jar -Doak.mongo.uri=mongodb://${mongodb.host}:${mongodb.port} $SLING_OPTS 