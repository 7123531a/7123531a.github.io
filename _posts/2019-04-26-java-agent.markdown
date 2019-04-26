[Java Agent Javadoc](https://docs.oracle.com/javase/8/docs/api/java/lang/instrument/package-summary.html)

# 1. Start agent

There are two ways to start agent.
* command-line interface

    **-javaagent:**jarpath[=options]
    
* Starting Agents After VM Startup

    An implementation may provide a mechanism to start agents sometime after the VM started. see below sample to load management agent after VM has started.

```java
private static void loadManagementAgent(VirtualMachine vm) throws IOException {
        String home = vm.getSystemProperties().getProperty("java.home");

        // Normally in ${java.home}/jre/lib/management-agent.jar but might
        // be in ${java.home}/lib in build environments.

        String agent = home + File.separator + "jre" + File.separator +
                "lib" + File.separator + "management-agent.jar";
        File f = new File(agent);
        if (!f.exists()) {
            agent = home + File.separator + "lib" + File.separator +
                    "management-agent.jar";
            f = new File(agent);
            if (!f.exists()) {
                throw new IOException("Management agent not found");
            }
        }

        agent = f.getCanonicalPath();
        try {
            vm.loadAgent(agent, "com.sun.management.jmxremote");
        } catch (AgentLoadException x) {
            IOException ioe = new IOException(x.getMessage());
            ioe.initCause(x);
            throw ioe;
        } catch (AgentInitializationException x) {
            IOException ioe = new IOException(x.getMessage());
            ioe.initCause(x);
            throw ioe;
        }
    }
```
# 2. Agent implementation requirements
* command-line interface

The agent class must implement a public static premain method similar in principle to the main application entry point.
The premain method has one of two possible signatures. The JVM first attempts to invoke the following method on the agent class:
```java
public static void premain(String agentArgs, Instrumentation inst);
```
If the agent class does not implement this method then the JVM will attempt to invoke:
```java
public static void premain(String agentArgs);
```
* Starting Agents After VM Startup

The agent class must implement a public static agentmain method similar in principle to the main application entry point.
The agentmain method has one of two possible signatures. The JVM first attempts to invoke the following method on the agent class:
```java
public static void agentmain(String agentArgs, Instrumentation inst);
```
If the agent class does not implement this method then the JVM will attempt to invoke:
```java
public static void agentmain(String agentArgs);
```

Each agent is passed its agent options via the **agentArgs** parameter. The agent options are passed as a single string, any additional parsing should be performed by the agent itself.

# 3. Manifest Attributes

The manifest of the agent jar must contains the attribute either **Premain-Class** or **Agent-Class**, or both.
When the agent is started on the command-line using the **-javaagent** option then the **Premain-Class** attribute specifies the name of the agent class and the **Agent-Class** attribute is ignored. Similarly, if the agent is started sometime after the VM has started, then the **Agent-Class** attribute specifies the name of the agent class (the value of **Premain-Class** attribute is ignored).

Manifest customization
```xml
 <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.0.0</version>
                <configuration>
                    <archive>
                        <index>true</index>
                        <manifest>
                            <addClasspath>true</addClasspath>
                        </manifest>
                        <manifestEntries>
                            <Agent-Class>agent.DummyAgent</Agent-Class>
                            <Premain-Class>agent.DummyAgent</Premain-Class>
                        </manifestEntries>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
```
