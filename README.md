**今后会发布更多反爬虫文章，点波关注不迷路哦。**

---
相信很多小伙伴扒某些网站的前端代码，打开控制台要看```Network```，结果发现他们页面一打开控制台就不断的```debugger```，完全运行调试。

如下图：
![进入调试，自动跳转到循环Debugger.png](https://upload-images.jianshu.io/upload_images/14530364-6ca3ff1568be9756.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**接下来分享两种解决方案**
---


####方案一
**右击```debugger ```行数位置，点击```add conditional breakpoint..```**
![点击add conditional breakpoint.png](https://upload-images.jianshu.io/upload_images/14530364-82db608178306042.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**添加```false```，然后按回撤， 刷新网页，发现成功跳过无限```debugger```**
![修改成false.png](https://upload-images.jianshu.io/upload_images/14530364-9a33ced6f0c9bec9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
####方案二
**1、在js文件右击， 然后点击```save as ...```, 把js文件保存到本地。**
![保存js文件到桌面.png](https://upload-images.jianshu.io/upload_images/14530364-9e8960a8fa62e16a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2、修改保存到本地的js文件，将```debugger```成```false ```保存js文件**
![修改debugger成false 保存js文件.png](https://upload-images.jianshu.io/upload_images/14530364-3b6b1ae35ac1cf07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3、在浏览器```Soures```获取需要替换的js文件```url```**
![获取需要替换的js文件url.png](https://upload-images.jianshu.io/upload_images/14530364-7a5b0814d95144a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**4、使用fiddler动态拦截替换js，刷新网页即可**
![使用fiddler动态拦截替换js.png](https://upload-images.jianshu.io/upload_images/14530364-6fae4217580ffa59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**如果文章对你有帮助，点个♥吧**



