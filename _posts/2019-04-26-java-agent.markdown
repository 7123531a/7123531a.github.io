[java agent description!](https://docs.oracle.com/javase/8/docs/api/java/lang/instrument/package-summary.html)

There are two ways to start agent.
* command-line interface
    **-javaagent:**jarpath[=options]
    
* Starting Agents After VM Startup

An implementation provides a mechanism to start agents sometime after the VM started. see below sample to load management agent after VM has started.

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