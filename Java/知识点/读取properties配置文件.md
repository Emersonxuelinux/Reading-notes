###读取properties配置文件

####PropertiesUtil.java、template.properties、Main.java处于同一目录
####1、PropertiesUtil.javatemplate.properties类
```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;

/**
 * Created by jaer on 2016/10/10.
 * 读取配置文件类
 */
public class PropertiesUtil {
    private static Properties prop;
    private static PropertiesUtil instance = new PropertiesUtil();

    /** 文件名称 */
    private String proFileStr = "template";

    public PropertiesUtil() {
        //properties文件的路径
        String templatePath = System.getProperty("user.dir") + File.separator + "src\\" + proFileStr + ".properties";
        if (prop == null) {
            prop = new Properties();
            File f = new File(templatePath);
            FileInputStream in = null;
            try {
                in = new FileInputStream(f);
                prop.load(in);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public static synchronized PropertiesUtil getInstance() {
        if (prop == null) {
            instance = new PropertiesUtil();
        }
        return instance;
    }

    public String getProperties(String key, String defaultVal) {
        return prop.getProperty(key) == null ? defaultVal : prop.getProperty(key);
    }
}

```

####2、template.properties文件
```
usname=root
password=123456
```

####3、测试类Main.java文件
```java
public class Main {
    public static void main(String[] args) {
        String usname = PropertiesUtil.getInstance().getProperties("usname","zhangsan");
        System.out.println(usname);
    }
}
```

输出
```
#如果没有获取到相应的值，使用默认值
root
```
