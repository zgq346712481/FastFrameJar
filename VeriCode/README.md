# VeriCode



## 1.简介
&emsp;Java图形验证码，支持gif、中文、算术等类型，可用于Java Web、JavaSE等项目。

---

## 2.效果展示

![验证码](https://s2.ax1x.com/2019/08/23/msFrE8.png) 
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msF0DP.png)
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msFwut.png)
<br/>
![验证码](https://s2.ax1x.com/2019/08/23/msFzVK.gif) 
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msFvb6.gif)
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msFXK1.gif)

**算术类型：**

![验证码](https://s2.ax1x.com/2019/08/23/mskKPg.png)
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msknIS.png)
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/mskma8.png)

**中文类型：**

![验证码](https://s2.ax1x.com/2019/08/23/mskcdK.png)
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msk6Z6.png)
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msksqx.png)

**内置字体：**

![验证码](https://s2.ax1x.com/2019/08/23/msAVSJ.png)
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msAAW4.png)
&emsp;&emsp;
![验证码](https://s2.ax1x.com/2019/08/23/msAkYF.png)


---

## 3.集成到项目

### 3.1 下载 jar 包到本地

https://github.com/YouAreOnlyOne/FastFrameJar/tree/master/VeriCode

里面的 vericode.jar 下载到本地。


### 3.2 maven方式引入

把下载的jar包放入本地maven仓库；然后在项目的 pom.xml 文件中添加如下的依赖：

```

<dependencies>
   <dependency>
      <groupId>com.ycj.fastframe</groupId>
      <artifactId>vericode</artifactId>
      <version>1.0</version>
   </dependency>
</dependencies>

```

### 3.3 lib方式引入

1）传统的SSM框架的Spring MVC 项目，在 WEB-INF 目录下建立 lib 目录，并把jar包复制到 lib 目录；然后在项目的 pom.xml 文件中添加如下的依赖：

```

  <dependency>
    <groupId>vericode</groupId>
    <artifactId>vericode</artifactId>
    <version>1.0</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/src/main/webapp/WEB-INF/lib/vericode.jar</systemPath>
  </dependency>

```

2）基于SpringBoot构建的项目，在 resources 目录下建立 lib 目录，并把jar包复制到 lib 目录；然后在项目的 pom.xml 文件中添加如下的依赖：

```
        <dependency>
            <groupId>vericode</groupId>
            <artifactId>vericode</artifactId>
            <version>1.0</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/src/main/resources/lib/vericode.jar</systemPath>
        </dependency>

```

---

## 4.使用方法

### 4.1.在SpringMVC中使用
```java
//使用默认字体
@Controller
public class CaptchaController {
    
    @RequestMapping("/captcha")
    public void captcha(HttpServletRequest request, HttpServletResponse response) throws Exception {
        CaptchaUtil.out(request, response);
    }
}

//使用指定字体
@Controller
public class VeriCodeController {
    @RequestMapping("/captcha")
    public void captcha(HttpServletRequest request, HttpServletResponse response) throws Exception {
        SpecCaptcha captcha = new SpecCaptcha(130, 48, 5);

        // 设置独立内置字体
        captcha.setFont(Captcha.FONT_7);
        // 设置操作系统字体
        // captcha.setFont(new Font("楷体", Font.PLAIN, 28));
        CaptchaUtil.out(captcha,request, response);
    }
}

```
前端html代码：
```html
<img src="/captcha" width="130px" height="48px" />
```

> 如果使用了权限管理或者拦截器，不要忘了把`/captcha`路径排除登录拦截，比如shiro的拦截。

### 4.2.在servlet中使用
web.xml中配置servlet：
```xml
<web-app>
    <!-- 图形验证码servlet -->
    <servlet>
        <servlet-name>CaptchaServlet</servlet-name>
        <servlet-class>com.wf.captcha.servlet.CaptchaServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>CaptchaServlet</servlet-name>
        <url-pattern>/captcha</url-pattern>
    </servlet-mapping>
</web-app>

```
前端html代码：
```html
<img src="/captcha" width="130px" height="48px" />
```

### 4.3.判断验证码是否正确

```java
@Controller
public class LoginController {
    
    @PostMapping("/login")
    public JsonResult login(String username,String password,String verCode){
        if (!CaptchaUtil.ver(verCode, request)) {
            CaptchaUtil.clear(request);  // 清除session中的验证码
            return JsonResult.error("验证码不正确");
        }
    }   
}
```

### 4.4.设置宽高和位数
```java
@Controller
public class CaptchaController {
    
    @RequestMapping("/captcha")
    public void captcha(HttpServletRequest request, HttpServletResponse response) throws Exception {
        // 设置位数
        CaptchaUtil.out(5, request, response);
        // 设置宽、高、位数
        CaptchaUtil.out(130, 48, 5, request, response);
        
        // 使用gif验证码
        GifCaptcha gifCaptcha = new GifCaptcha(130,48,4);
        CaptchaUtil.out(gifCaptcha, request, response);
    }
}
```

### 4.5.不使用工具类
&emsp;CaptchaUtil封装了输出验证码、存session、判断验证码等功能，也可以不使用此工具类：

```java
@Controller
public class CaptchaController {
    
    @RequestMapping("/captcha")
    public void captcha(HttpServletRequest request, HttpServletResponse response) throws Exception {
        // 设置请求头为输出图片类型
        response.setContentType("image/gif");
        response.setHeader("Pragma", "No-cache");
        response.setHeader("Cache-Control", "no-cache");
        response.setDateHeader("Expires", 0);
        
        // 三个参数分别为宽、高、位数
        SpecCaptcha specCaptcha = new SpecCaptcha(130, 48, 5);
        // 设置字体
        specCaptcha.setFont(new Font("Verdana", Font.PLAIN, 32));  // 有默认字体，可以不用设置
        // 设置类型，纯数字、纯字母、字母数字混合
        specCaptcha.setCharType(Captcha.TYPE_ONLY_NUMBER);
        
        // 验证码存入session
        request.getSession().setAttribute("captcha", specCaptcha.text().toLowerCase());
        
        // 输出图片流
        specCaptcha.out(response.getOutputStream());
    }
    
    @PostMapping("/login")
    public JsonResult login(String username,String password,String verCode){
        // 获取session中的验证码
        String sessionCode = request.getSession().getAttribute("captcha");
        // 判断验证码
        if (verCode==null || !sessionCode.equals(verCode.trim().toLowerCase())) {
            return JsonResult.error("验证码不正确");
        }
    }  
}
```

## 5.更多设置

### 5.1.验证码类型

```java
public class Test {
    
    public static void main(String[] args) {
        // png类型
        SpecCaptcha captcha = new SpecCaptcha(130, 48);
        captcha.text();  // 获取验证码的字符
        captcha.textChar();  // 获取验证码的字符数组
        
        // gif类型
        GifCaptcha captcha = new GifCaptcha(130, 48);
        
        // 中文类型
        ChineseCaptcha captcha = new ChineseCaptcha(130, 48);
        
        // 中文gif类型
        ChineseGifCaptcha captcha = new ChineseGifCaptcha(130, 48);
        
        // 算术类型
        ArithmeticCaptcha captcha = new ArithmeticCaptcha(130, 48);
        captcha.setLen(3);  // 几位数运算，默认是两位
        captcha.getArithmeticString();  // 获取运算的公式：3+2=?
        captcha.text();  // 获取运算的结果：5
        
        captcha.out(outputStream);  // 输出验证码
    }
}
```

> 注意：<br/>
> &emsp;算术验证码的len表示是几位数运算，而其他验证码的len表示验证码的位数，算术验证码的text()表示的是公式的结果，
> 对于算术验证码，你应该把公式的结果存储session，而不是公式。

### 5.2.验证码字符类型

 类型 | 描述 
 :--- | :--- 
 TYPE_DEFAULT | 数字和字母混合 
 TYPE_ONLY_NUMBER | 纯数字
 TYPE_ONLY_CHAR | 纯字母 
 TYPE_ONLY_UPPER | 纯大写字母
 TYPE_ONLY_LOWER | 纯小写字母
 TYPE_NUM_AND_UPPER | 数字和大写字母

使用方法：
```
SpecCaptcha captcha = new SpecCaptcha(130, 48, 5);
captcha.setCharType(Captcha.TYPE_ONLY_NUMBER);
```

> 只有`SpecCaptcha`和`GifCaptcha`设置才有效果。

### 5.3.字体设置
内置字体：

 字体 | 效果 
 :--- | :--- 
 Captcha.FONT_1 |  ![](https://s2.ax1x.com/2019/08/23/msMe6U.png)
 Captcha.FONT_2 | ![](https://s2.ax1x.com/2019/08/23/msMAf0.png)
 Captcha.FONT_3 |  ![](https://s2.ax1x.com/2019/08/23/msMCwj.png)
 Captcha.FONT_4 | ![](https://s2.ax1x.com/2019/08/23/msM9mQ.png)
 Captcha.FONT_5 | ![](https://s2.ax1x.com/2019/08/23/msKz6S.png)
 Captcha.FONT_6 | ![](https://s2.ax1x.com/2019/08/23/msKxl8.png)
 Captcha.FONT_7 | ![](https://s2.ax1x.com/2019/08/23/msMPTs.png)
 Captcha.FONT_8 | ![](https://s2.ax1x.com/2019/08/23/msMmXF.png)
 Captcha.FONT_9 | ![](https://s2.ax1x.com/2019/08/23/msMVpV.png)
 Captcha.FONT_10 | ![](https://s2.ax1x.com/2019/08/23/msMZlT.png)

使用方法：
```
SpecCaptcha captcha = new SpecCaptcha(130, 48, 5);

// 设置内置字体
captcha.setFont(Captcha.FONT_1); 

// 设置系统字体
captcha.setFont(new Font("楷体", Font.PLAIN, 28)); 
```

### 5.4.输出base64编码
```
SpecCaptcha specCaptcha = new SpecCaptcha(130, 48, 5);
specCaptcha.toBase64();

// 如果不想要base64的头部data:image/png;base64,
specCaptcha.toBase64("");  // 加一个空的参数即可
```

### 5.5.输出到文件
```
FileOutputStream outputStream = new FileOutputStream(new File("C:/captcha.png"))
SpecCaptcha specCaptcha = new SpecCaptcha(130, 48, 5);
specCaptcha.out(outputStream);
```

---

## 6.前后端分离项目的使用

&emsp;前后端分离项目建议不要存储在session中，存储在redis中，redis存储需要一个key，key一同返回给前端用于验证输入：
```java
@Controller
public class CaptchaController {
    @Autowired
    private RedisUtil redisUtil;
    
    @ResponseBody
    @RequestMapping("/captcha")
    public JsonResult captcha(HttpServletRequest request, HttpServletResponse response) throws Exception {
        SpecCaptcha specCaptcha = new SpecCaptcha(130, 48, 5);
        String verCode = specCaptcha.text().toLowerCase();
        String key = UUID.randomUUID().toString();
        // 存入redis并设置过期时间为30分钟
        redisUtil.setEx(key, verCode, 30, TimeUnit.MINUTES);
        // 将key和base64返回给前端
        return JsonResult.ok().put("key", key).put("image", specCaptcha.toBase64());
    }
    
    @ResponseBody
    @PostMapping("/login")
    public JsonResult login(String username,String password,String verCode,String verKey){
        // 获取redis中的验证码
        String redisCode = redisUtil.get(verKey);
        // 判断验证码
        if (verCode==null || !redisCode.equals(verCode.trim().toLowerCase())) {
            return JsonResult.error("验证码不正确");
        }
    }  
}
```
前端使用ajax获取验证码：
```html
<img id="verImg" width="130px" height="48px"/>

<script>
    var verKey;
    // 获取验证码
    $.get('/captcha', function(res) {
        verKey = res.key;
        $('#verImg').attr('src', res.image);
    },'json');
    
    // 登录
    $.post('/login', {
        verKey: verKey,
        verCode: '8u6h',
        username: 'admin'，
        password: 'admin'
    }, function(res) {
        console.log(res);
    }, 'json');
</script>
```

> RedisUtil 工具类到这里获取：

https://github.com/YouAreOnlyOne/FastFrameJar/tree/master/Utils/RedisUtil


---

## 7.自定义效果

&emsp;继承`Captcha`实现`out`方法，中文验证码可继承`ChineseCaptchaAbstract`，算术验证码可继承`ArithmeticCaptchaAbstract`。

---

## 8.简要描述


    - 10种漂亮的内置字体，不依赖系统字体
    
    - 算术验证码，运算位数可自由配置

    - 可以输出base64编码的功能

    - 贝塞尔曲线作为干扰线

    - 纯大写字母、纯小写字母、数字和大写字母配置

    - 支持中文验证码、中文gif验证码

    - 抗锯齿效果，优化文字颜色

    - CaptchaUtil 工具类便于Web项目使用
