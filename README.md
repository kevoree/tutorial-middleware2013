keynote for Middleware 13 tutorial
=======

# Kevoree Hands-on tutorial

a documentation of Kevoree is available [here](http://kevoree.org/doc)

## Step 0: Configure you environments
1. Just unzip the archive that corresponds to your architecture. Be careful, it takes 2GB. It can be less in reoving the IDE and JVM if you get one. 
2. go to the folder.
3. Setup the environement 
```bash
source env.sh | env.bat
```
4. Test the editor
```bash
java -jar editor.jar
```
5. Test the platform
```bash
java -jar platform-gui.jar
```


## Step 1: First distributed applications

### Step 1.a: First applications
1. Test the editor
```bash
java -jar editor.jar
```
2. Test the platform
```bash
java -jar platform-gui.jar
```
3. Import Java toy and channel library -> Model -> Load Kevoree Library -> Java -> Channel/Toy (3.0.2)
4. Create a model  2 ToyConsoles bound through an AsyncBroadCast channel
5. Push the model
6. Test the application

### Step 1.b: First distributed applications
1. Start a second Java container
```bash
java -Dnode.name=node1 -Dnode.bootstrap=node1.kevs -jar platform.jar 
```
2. Merge the model of this new container (File -> Merge from node (localhost:9001))
3. Import Java toy and HazelCast library (Model -> Load Kevoree Library -> Java -> HazelCast/Toy)
4. Update your first model -> 2 ToyConsoles bound through an DistributedBroadCast
5. Push the model
6. Test the application

### Step 1.c: First distributed web applications
1. Import Java web library (Model -> Load Kevoree Library -> Java -> HazelCast/Toy)
2. Update your first model -> 2 ToyConsoles bound through an DistributedBroadCast
3. Push the model
4. Test the application

You can find documentation [here](kevoree.org/practices/level1)


## Step 2: Develop with Kevoree: From source code to deployment 
1. Start your favorite IDE (if you do not have take eclipse. 
```bash
source env.sh
eclipse
```
2. Import the Producer/Consumer example from kevoree-samples/ProducerConsumer/org.kevoree.library.java.producerconsumer/
(File -> Import existing maven project -> ...)
3. Fill the project XXXX
4. Compile the project mvn clean install
5. Load the project in the editor (Model -> Load Library -> chose the jar compiled in the kevoree-samples/ProducerConsumer/org.kevoree.library.java.producerconsumer/target folder
6. Create a new model with your component
7. Test the application

You can find documentation [here](kevoree.org/practices/level2)


## Step 3: Runtime adaptation using Kevoree 
1. in the org.kevoree.library.java.producerconsumer project. Adapt the behavior of a component to be removed if it has not received any message since 1 min

```java
//TODO Code to show
```

You can find documentation [here](kevoree.org/practices/level3)

# Step 4: Kevoree for Managing cloud 
1. Follow the tutorial [here](kevoree.org/practices/level4) for this step

