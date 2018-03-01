# How to open JMX ports

JVM options to be set:

```
   -Djavax.management.builder.initial= 
   -Dcom.sun.management.jmxremote 
   -Dcom.sun.management.jmxremote.port=8855 
   -Dcom.sun.management.jmxremote.authenticate=false 
   -Dcom.sun.management.jmxremote.ssl=false
```

See [this](https://stackoverflow.com/questions/856881/how-to-activate-jmx-on-my-jvm-for-access-with-jconsole/856882)