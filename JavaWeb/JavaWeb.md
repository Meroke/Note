```xml
 # web.xml 设置 servelet 启动顺序,value 越小，越先启动，最小0
<load-on-startup>1</load-on-startup> 
```

项目配置 Tomcat:

要注意步骤1，是点击文件夹，才能出现下图。

==*如果先配置了Web，则需要在注意别点击到Web，要点击项目文件夹*==

![image-20220925204113688](E:\File\TyporaNote\img\JavaWeb\image-20220925204113688.png)



项目配置WEB：

![image-20220925203859717](E:\File\TyporaNote\img\JavaWeb\image-20220925203859717.png)



![image-20220925203920841](E:\File\TyporaNote\img\JavaWeb\image-20220925203920841.png)

![image-20220925203930533](E:\File\TyporaNote\img\JavaWeb\image-20220925203930533.png)





```
//Servlet从3.0版本开始支持注解方式的注册
@WebServlet("/index")  # 使用此形式可以完全舍弃 web.xml
public class IndexServlet extends ViewBaseServlet {
    @Override
    public void doGet(HttpServletRequest request , HttpServletResponse response)throws IOException, ServletException {
        
    }
}
```



在web.xml中

```
    <context-param>
        <param-name>view-prefix</param-name>
        <param-value>/</param-value>  # 这里的路径就是web
    </context-param>
```

![image-20220925210152440](E:\File\TyporaNote\img\JavaWeb\image-20220925210152440.png)

## 去哪玩配置003

### 1.Tomcat:

![image-20220925212637739](E:\File\TyporaNote\img\JavaWeb\image-20220925212637739.png)



### 需要注意下，Application context 是网址目录，，如下图所示，配合上图的URL设置，真实网址为`http://localhost:8080/PRJ_WTP_JEE_003/`

![image-20220925212653114](E:\File\TyporaNote\img\JavaWeb\image-20220925212653114.png)



### 检查Modules 里的web配置，所有的path需要指向项目根目录下的web文件夹的内容，IDEA会自动指向.idea里自动生成的web文件夹，需要仔细注意。

![image-20220925212922392](E:\File\TyporaNote\img\JavaWeb\image-20220925212922392.png)



### 生成的Artifas 的Name无需改变，只需要tomcat里的appplication Context里简短点即可。

![image-20220925213133493](E:\File\TyporaNote\img\JavaWeb\image-20220925213133493.png)