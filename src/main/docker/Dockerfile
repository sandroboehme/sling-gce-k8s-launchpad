FROM java:8
MAINTAINER sandro.boehme@gmx.de
COPY ./sample-launchpad-${project.version}.jar /opt/sling/
WORKDIR /opt/sling/
EXPOSE 8080
VOLUME /opt/sling/sling
# The os.version of the GKE image used is broken. It returns '4.4.35.' which the wrong format that the Felix framework (5.6.4) cannot parse.
# org.apache.felix.framework.Felix.initializeFrameworkProperties(Felix.java:4617) reads 'os.version' and
# org.apache.felix.framework.util.manifestparser.NativeLibraryClause.normalizeOSVersion(NativeLibraryClause.java:770) initializes the 'Version'
# class that fails to parse the wrong format the Google 'cos' image provides. 
ENV JAVA_OPTS '-Xmx512m -Dos.version=4.4.35'
ENV _JAVA_OPTIONS '-Dos.version=4.4.35'
ENV SLING_OPTS '-Dos.version=4.4.35'
ENV MONGO_HOST 'mongo'
ENV MONGO_PORT '27017'
CMD exec java $JAVA_OPTS -jar sample-launchpad-${project.version}.jar -Dos.version=4.4.35 -Doak.mongo.uri=mongodb://$MONGO_HOST:$MONGO_PORT $SLING_OPTS