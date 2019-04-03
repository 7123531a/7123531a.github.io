Apache Velocity is a great template parsing tool. it can be used to generate source code based on template.

Usage pattern:

Java

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

Template

```html
<style>
table, th, td {
  border: 1px solid black;
  border-collapse: collapse;
}
th, td {
  padding: 5px;
  text-align: left;
}
</style>
#foreach( $key in $status.keySet() )
    <table>
        <caption>$key</caption>
        <tr>
            <th>
                Time
            </th>
            <th>
                NODE
            </th>
            <th>
                API
            </th>
            <th>
                METHOD
            </th>
            <th>
                MSG
            </th>
            <th>
                STATUS
            </th>
        </tr>
    #foreach( $event in $status.get($key) )
        #set( $task = $event.getSource() )
        <tr>
            <td>
                $event.getDatetime()
            </td>
            <td>
                $task.getNode()
            </td>
            <td>
                $task.getServiceAPI()
            </td>
            <td>
                $task.getMethodname()
            </td>
            <td>
                $event.getMsg()
            </td>
            <td>
                $event.getStatus()
            </td>
        </tr>
    #end
    </table>
#end
```
