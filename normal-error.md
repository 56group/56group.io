# 常见错误调整

##### tomcat X11Manager Error

```
# bin/catalina.sh
CATALINA_OPTS=-Djava.awt.headless=true
shell> yum install libgcc.i686 --setopt=protected_multilib=false
shell> yum grouplist
shell> yum groupinstall Fonts
```

```
public class XllManagerUtil {
    static {
        System.setProperty("java.awt.headless", "true");
    }
}
```





