Apache Velocity is a great template parsing tool. it can be used to generate source code based on template.

Usage pattern:

```java

VelocityEngine ve = new VelocityEngine();
ve.init();

Reader reader = new InputStreamReader(getClass().getResourceAsStream("/template.html"));

Context context = new VelocityContext();
context.put("status", eventStore.getEvents());

Writer writer = new StringWriter();
ve.evaluate(context, writer, "", reader);

System.out.println(writer.toString());

```
