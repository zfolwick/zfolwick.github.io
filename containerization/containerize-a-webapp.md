 
Create a hello-world service
run the following command to create a new maven project: mvn archetype:generate -DgroupId=com.howdy -DartifactId=hello-service -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
follow along with this tutorial: Spring Boot Hello World RESTful Web Services Tutorial .
Changes to make:
change the package name to com.howdy in the HelloController.java.
Place the HelloController.java in the same directory as App.java.
Either rename the file App.java to Application.java, or rename the class name pasted into the main method to “App.class”.
 
Containerize the application
Create the Container definition
Create a Dockerfile in the project root directory.
Copy the following into the dockerfile:



FROM maven:3.6.3-openjdk-17 as build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -Dmaven.test.skip=true

FROM openjdk:17-jdk-alpine as run
LABEL description="Image for running the cookie service"
ENV MAVEN_OPTS="-Xmx512m -Djava.awt.headless=true"
ENV TERM="xterm-256color"
WORKDIR /app
COPY --from=build /app/target/*.jar /app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
EXPOSE 8080
Explanation:
This docker file defines two stages: a build stage, and a run stage.
The build stage uses the extremely lightweight docker image maven:3.6.3-openjdk-17to copy resources from the host machine over to the container and run the command to create a jar file within the target directory: mvn clean package -Dmaven.test.skip=true
The run stage uses the even more lightweight docker image openjdk:17-jdk-alpine to run the jar file.  First, it copies the build artifacts from from the build stage’s working directory, renames them to /app.jar then, it sets the entrypoint to run java -jar /app.jar whenever docker run is called.
It then exposes the docker port 8080 to the host.
Run the container
In the project’s root directory, first we build the image from the Dockerfile with docker build -t hello-service .
if you keep your docker files in a separate directory (let’s say- src/docker), you can run with a -f flag: docker build -t hello-service . -f docker/Dockerfile.yaml.
you may now run the image using docker run -it -p 8080:8080 hello-service, and visit http://localhost:8080/hello to see your service running.
 
You have now successfully containerized your webapp!

Now What?  Sharing with others
Creating the image with docker build is the first step.  Next needs to be pushing to a cloud service.  Check out some of the really good documentation out there to be able to share your new image with others.

