FROM websphere-liberty:javaee7
# Install Maven
RUN apt-get update /
  && apt-get -y install maven

ENV JAVA_VER=8 \
    JAVA_REL=0 \
    JAVA_MF=3.0

#Install IBM Java SDK
RUN rm -r /opt/ibm/java/ \
  && mkdir -p /opt/s2i/destination \
  && TARGET_ARCH=$(uname -m) \
  && JAVA_VRMF=$JAVA_VER.$JAVA_REL-$JAVA_MF \
  && YML_FILENAME=ibm-java-sdk-$JAVA_VRMF-linux-$TARGET_ARCH-javase.yml \
  && BASE_URL="https://public.dhe.ibm.com/ibmdl/export/pub/systems/cloud/runtimes/java/meta" \
  && wget -nv -U UA-IBM_JAVA_Docker -O /tmp/ibm-java.yml $BASE_URL/$JAVA_VER$JAVA_REL/$YML_FILENAME \
  && JAVA_URL=$(cat /tmp/ibm-java.yml | sed -n 's/\s*uri:\s//p' | tr -d '\r') \
  && wget -nv -U UA-IBM-JAVA-Docker -O /tmp/ibm-java.bin $JAVA_URL \
  && ESUM=$(cat /tmp/ibm-java.yml | sed -n 's/\s*md5sum:\s//p' | tr -d '\r') \
  && echo "$ESUM /tmp/ibm-java.bin" | md5sum -c - \
  && rm -f /tmp/ibm-java.yml \
  && echo "INSTALLER_UI=silent" > /tmp/response.properties \
  && echo "USER_INSTALL_DIR=/opt/ibm/java" >> /tmp/response.properties \
  && echo "LICENSE_ACCEPTED=TRUE" >> /tmp/response.properties \
  && mkdir -p /opt/ibm \
  && chmod +x /tmp/ibm-java.bin \
  && /tmp/ibm-java.bin -i silent -f /tmp/response.properties \
  && rm -f /tmp/response.properties \
  && rm -f /tmp/ibm-java.bin
ENV JAVA_HOME=/opt/ibm/java \
    PATH=/opt/ibm/java/bin:$PATH

LABEL io.k8s.description="Platform for building and running JEE applications on WebSphere Liberty" \
      io.k8s.display-name="WebSphere Liberty" \
      io.openshift.expose-services="9080:http,9443:https" \
      io.openshift.tags="builder,liberty,websphere-liberty" \
      io.openshift.s2i.destination="/opt/s2i/destination" \
	  io.openshift.s2i.scripts-url="image:///opt/s2i/"

# Copy Scripts to build s2i
COPY assemble /opt/s2i/
COPY run /opt/s2i/
COPY usage /opt/s2i/
