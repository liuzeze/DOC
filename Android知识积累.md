##### launchmode   TaskAffinity

首先说四种启动模式   standard  singleTop singleTask singleInstance  ,Taskaffinity一般和singleTask结合使用,开启一个新的任务栈,可以将taskAffinity相同的组件放在同一栈中  区别于singleInstance的是   singleTask方式开启的栈中可以放置多个组件 而 singleInstance中只能有一个实例,TaskAffinity的作用就是起到共享任务栈

##### res/raw 和Asserts区别

两者都会将源文件打包到apk中, res/raw 会生成R文件引用  ,而 asserts只能 通过 AssertManager 来加载使用目录下的资源,assert下可以有目录嵌套 raw不可以嵌套

##### mipmap-nodpi  

如果有套图可以放到不同目录下 ,如果只有一张放到最高分辨率的目录下放置出现高密度手机加载图片是内存过高情况,因为系统会对图片进行一定放缩,  nodpi这个文件系统不会对这个文件下的资源进行放缩,直接加载原始尺寸,在这个目录下放了之后其他目录下 不哟啊再放     此目录的优先级最低

##### Bitmap和Drawable的区别

Bitmap是一种内存中的数据结构 包含和像素  宽高 缩放比例等属性,Bitmap获取只能通过  BitmapFactory从jni中加载  ,而drawable 可以来自Bitmap的Bitmapdrawable  也可以来自shape的drawable, 类似于一种绘制的操作  只描述 图片的颜色 大小 数据 图形,drawble类似于view的绘制 但是 不会跟用户有操作者

##### 加载大图巨图处理方式

加载大图,可以多图片进行放缩   通过bitmapfactory.option  和 injustdecidebound  来获取原始图片的宽高,然后算出合适的比例 赋值给insamplesize  按照这个比例再重新加载合适的图片,,如果图片较多 可以做好内存中的管理 使用异步加载  和使用内存缓存策略,如果加载巨图可以使用BitmapRegionDecoder  来分块加载   期间使用要手势滑动器gestureDetector  来对图片进行重绘移动

##### 圆角图片的生成



