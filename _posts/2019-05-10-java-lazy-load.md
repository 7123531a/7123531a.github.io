```java
public class Main {
    public static void main(String[] args) {
        System.out.println("start");
        System.out.println(Holder.value);
    }

    static class Holder {
        static final int value;

        static {
            System.out.println("Number will be printed only if Holder.value is accessed");
            value = 10;
        }
    }
}
```
