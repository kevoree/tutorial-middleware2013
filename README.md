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

This editor can serve to design an architecture. 

From the file menu, you can load or save a model in different formalisms (xmi (model) or kevscript (text-based), get a model of a running system. 
From the model menu, you can clear the current design model, check the current design model, or load libraries that contains component/channel/node type description  from existing repository of from a Jar file. 
From the kevscript menu, you can open a kevscript editor to execute reconfiguration script on your running architecture. 
From the tool menu, you can:

* open a serial monitor (useful when you work with arduino)
* open an XMI editor, (useful to debug)
* select the logging level

In the editor, you get also three buttons:

* to get the editor console
* to get the checker console (to check your architecture consistency)
* to get a kevscript console 


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
3. To get model from a node, go to (File → Open From Node). A new popup appears allowing you to define how to communicate with the group on your platform.Once you set the ip and the port number, you can validate with the “OK” button. The editor pulls the model to the group and ten display it.
4. Import Java toy and channel library -> Model -> Load Kevoree Library -> Java -> Channel/Toy (3.0.2)
5. Create a model  2 ToyConsoles bound through an AsyncBroadCast channel
6. Push the model; Click on the node and click on the push button. This action sends the current model to all the nodes connected through the group. Each node will update its running architecture. 
7. Test the application

### Step 1.b: First distributed applications
1. Start a second Java container
```bash
java -Dnode.name=node1 -Dnode.bootstrap=node1.kevs -jar platform.jar 
```
2. Merge the model of this new container (File -> Merge from node (localhost:9001))
3. Import Java toy and HazelCast library (Model -> Load Kevoree Library -> Java -> HazelCast/Toy)
4. Update your first model -> 2 ToyConsoles bound through an DistributedBroadCast
5. Push the model. Click on the node and click on the push button. This action sends the current model to all the nodes connected through the group. Each node will update its running architecture. 
6. Test the application

### Step 1.c: First distributed web applications
1. Import Java web library (Model -> Load Kevoree Library -> Java -> HazelCast/Toy)
2. Update your first model -> 2 ToyConsoles bound through an DistributedBroadCast
3. Push the model
4. Test the application

You can find documentation [here](kevoree.org/practices/level1)


## Step 2: Develop with Kevoree: From source code to deployment 

Now you are able to manipulate the Kevoree tools so we can develop Kevoree stuff.
In this part, we propose to build a new MXDataProvider that doesn't generate random data but monitor your own computer (e.g. CPU, RAM) and send this data as the Random component.
As a project skeleton, we provide two projects, the first one is an example of Observer pattern, the second one is just a skeleton. The goal is to create a component that can send datas monitor from your computer. You can read the code of the Observer pattern to see how you can declare your component parameters and your ports. 

1. Start your favorite IDE (if you do not have take eclipse. 
```bash
source env.sh
eclipse
```
2. Import the Producer/Consumer example from kevoree-samples/ProducerConsumer/org.kevoree.library.java.producerconsumer/
(File -> Import existing project -> ...)
3. Fill the project BreakDown
4. You can use to monitor your component

```java
com.sun.management.OperatingSystemMXBean operatingSystemMXBean = (com.sun.management.OperatingSystemMXBean) ManagementFactory.getOperatingSystemMXBean()

operatingSystemMXBean.getSystemLoadAverage()
operatingSystemMXBean.getFreePhysicalMemorySize()

operatingSystemMXBean.getTotalPhysicalMemorySize()
```
5. Compile the project mvn clean install
6. Load the project in the editor (Model -> Load Library -> chose the jar compiled in the kevoree-samples/ProducerConsumer/org.kevoree.library.java.producerconsumer/target folder
7. Create a new model with your component. You can use a 
8. Test the application

You can find documentation [here](kevoree.org/practices/level2)


## Step 3: Runtime adaptation using Kevoree 
1. in the org.kevoree.library.java.producerconsumer project. Adapt the behavior of a component to be removed if it has not received any message since 1 min

```java
package org.kevoree.library.java.toys;

import org.kevoree.ContainerRoot;
import org.kevoree.annotation.*;
import org.kevoree.api.Context;
import org.kevoree.api.KevScriptService;
import org.kevoree.api.ModelService;
import org.kevoree.cloner.DefaultModelCloner;
import org.kevoree.log.Log;
import org.kevoree.modeling.api.ModelCloner;

import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.ScheduledFuture;
import java.util.concurrent.TimeUnit;

/**
 * Created with IntelliJ IDEA.
 * User: duke
 * Date: 06/12/2013
 * Time: 10:00
 */
@Library(name = "Java :: Toys")
@ComponentType
public class Breakdown implements Runnable {

    @Param(defaultValue = "3000")
    Long period = 3000l;

    private ScheduledExecutorService service;
    private ScheduledFuture current;

    @Start
    public void start() {
        service = Executors.newSingleThreadScheduledExecutor();
        current = service.schedule(this, period, TimeUnit.MILLISECONDS);
    }

    @Stop
    public void stop() {
        service.shutdownNow();
    }

    @Input
    public void input(Object o) {
        Log.info("a friend ! i'm fine thanks you!");
        current.cancel(true);
        current = service.schedule(this, period, TimeUnit.MILLISECONDS);
    }

    @KevoreeInject
    ModelService modelService;

    @KevoreeInject
    Context context;

    @KevoreeInject
    KevScriptService kevScriptService;

    @Override
    public void run() {
        try {
            Log.info("i'm alone... kill myself...");
            ModelCloner cloner = new DefaultModelCloner();
            ContainerRoot clonedModel = cloner.clone(modelService.getCurrentModel().getModel());
            kevScriptService.execute("remove " + context.getNodeName() + "." + context.getInstanceName(), clonedModel);
            modelService.update(clonedModel, null);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

You can find documentation [here](kevoree.org/practices/level3)

# Step 4: Kevoree for Managing cloud 
1. Follow the tutorial [here](kevoree.org/practices/level4) for this step

