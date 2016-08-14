FROM tomcat:8.5

MAINTAINER ProgramBuddy <info@programbuddy.com>

# maven install
RUN mkdir /opt/maven && \
    wget http://apache.osuosl.org/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz && \
    tar -xzvf apache-maven-3.3.9-bin.tar.gz -C /opt/maven && \
    rm apache-maven-3.3.9-bin.tar.gz
ENV PATH=/opt/maven/apache-maven-3.3.9/bin:$PATH

# generate web application
RUN mvn archetype:generate -DgroupId=group.id.test \
    	-DartifactId=artifact.id.web \
    	-DarchetypeArtifactId=maven-archetype-webapp \
    	-DinteractiveMode=false
RUN mvn clean install -f ./artifact.id.web
RUN cp ./artifact.id.web/target/artifact.id.web.war ./webapps
