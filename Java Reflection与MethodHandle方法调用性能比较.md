## 程序源码

```java
public class App {

    public static void main(String[] args) {
        final int times = 1000000;
        final Man man = new Man();
        final String msg = "hello";

        long t1 = System.currentTimeMillis();
        for (int i=0; i<times; i++) {
            try {
                Method m = man.getClass().getDeclaredMethod("say", String.class);
                m.setAccessible(true);
                m.invoke(man, msg);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        System.out.println("reflect cost " + (System.currentTimeMillis() - t1) + " ms");

        long t2 = System.currentTimeMillis();
        for (int i=0; i<times; i++) {
            try {
                MethodType mt = MethodType.methodType(void.class, String.class);
                MethodHandle handle = lookup().findVirtual(man.getClass(), "say", mt);
                handle.invokeExact(man, msg);
            } catch (Throwable e) {
                e.printStackTrace();
            }
        }
        System.out.println("invoke cost " + (System.currentTimeMillis() - t2) + " ms");

    }

    static class Man {
        void say(String m) {
        }
    }

}
```

## 运行结果
```
reflect cost 214 ms
invoke cost 1333 ms
```

## 结果
多次运行取平均值，Reflection比MethodHandle快了6倍
