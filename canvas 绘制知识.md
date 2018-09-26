

#### Canvas

1. drawColor  在之前的基础上绘制背景颜色  

2. drawRGB 

3. drawARGB 

4. drawCircle  画圆 传入中心点和半径

5. drawRect 传入上下左右间距和paint

6. drawPoint  画点    可根据paint的setStrokeCap参数  判断画圆点还是方点

7. drawPoints  传入集合  offset  偏移几个索引开始绘制  count  一共绘制几个像素点

8. drawOval  画椭圆

9. drawLine  画线传入起始点和画笔paint

10. drawLines 传入集合类似 drawpoints方法

11. drawRoundRect 圆角矩形   传入上下左右 以及 x y的圆角半径

12. drawArc 绘制弧形 传入弧形所在的椭圆的上下左右, 起始弧度(startAngle)  和划过的弧度(sweepAngle)    和是否连接圆心(useCenter)

13. drawPath 画自定义的图形
    1. path.addCircle 路径添加圆形  最后参数 CW 顺时针  CCW 逆时针
    2. path.addOval
    3. path.addRect
    4. path.addRoundRect
    5. path.addPath   
    6. path.lineTo 当前七点开始画线(绝对坐标)
    7. path.rLineTo   相对距离画线
    8. path.quadTo 二次贝塞尔曲线  ,传入控制点 和终点的坐标  起点绝对坐标
    9. path.rQuadTo  起点相对坐标
    10. path.cubicTo   三次贝塞尔曲线  传入两个控制点和终点  起点绝对坐标
    11. path.rCubicTo 起点相对坐标
    12. path.moveTo  移动当前的坐标
    13. path.arcTo  弧线绘制  不能是扇形   传入弧形所在的椭圆的上下左右, 起始弧度(startAngle)  和划过的弧度(sweepAngle) 最后参数 设置是否是从当前点拖过去绘制  还是直接跳转到绘制点
    14. path.addArc 
    15. path.close 是否封闭当前图形
    16. path.setFillType  设置填充模式  WINDING(有区分:顺时针时全部绘制,逆时针时不绘制重合部分)  EVEN_ODD(不绘制重合区域)  INVERSE_EVEN_OD  和  INVERSE_WINDING  与前两个相反

14. drawBitmap  参数有matrix  可以做图像便函  缩放 平移 旋转等

15. drawBitmapMesh  拉伸效果

16. drawText 

17. drawTextOnPath  在一个路径上绘制文字 路径不要尖角

18. StaticLayout  支持换行绘制文字

    `width` 是文字区域的宽度，文字到达这个宽度后就会自动换行；
    `align` 是文字的对齐方向；
    `spacingmult` 是行间距的倍数，通常情况下填 1 就好；
    `spacingadd` 是行间距的额外增加值，通常情况下填 0 就好；
    `includeadd` 是指是否在文字上下添加额外的空间，来避免某些过高的字符的绘制出现越界。

19. clipRect  范围剪裁  上下左右  save  restore   在剪裁钱保存绘制范围  剪裁后恢复绘制范围

20. clipPath  剪裁一个path路径

21. 几何变换   Canvas 常见二位变换  Matrix  常见和不常见的二维变换  Camera  做三维变换

22. translate  平移 参数是横向纵向平移

23. rotate 旋转 角度和旋转中心

24. scale  缩放   横向纵向放缩倍数  放缩中心

25. skew 错切  相当于倾斜扭转效果

26. concat  传入矩阵 会用当前的变换 和传入的结合相乘

27. setMatrix  设置新的举证变换

    1. stPolyToPoly  多点映射  参数: 两个集合是 原始点 和 变化后的点   ** index是第一个点的偏移  count  采集点的个数

28. Camera  有三类

    1. 旋转 `rotateX(deg)` `rotateY(deg)` `rotateZ(deg)` `rotate(x, y, z)`
    2. applayTocanvas  把旋转投影到canvas上
    3. 平移  translate
    4. 投影  setLocation   x  y 一般写0   高像素的手机 经过图形变换 (旋转等)图片会模糊 这个时候需要 将相机后移点距离  来保证图片的正确显示


#### Paint(画笔) 

1. setColor

2. setStyle  STROKE画线   FIll  填充  FILL_AND_STROKE 填充和画线

3. setAntiAlias  抗锯齿

4. setStrokrWidth  线条宽度

5. setStrokeCap  设置线头的形状   Paint.Cap.ROUND(圆)/SQARE/BUTT

6. setStrokeJoin 设置拐角形状  `MITER` 尖角、 `BEVEL` 平角和 `ROUND` 圆角

7. setStrokeMiter  设置拐角延长线的最大值

8. setShader(Shader shader)
   1. LinearGradient  线性渐变 `TileMode` 一共有 3 个值可选： `CLAMP`, `MIRROR` 和 `REPEAT`。
   2. RadialFradient 辐射渐变   TileMode` 一共有 3 个值可选： `CLAMP`, `MIRROR` 和 `REPEAT`。
   3. SweepGradient 扫描渐变 
   4. BitmapShader  图片着色  TileMode` 一共有 3 个值可选： `CLAMP`, `MIRROR` 和 `REPEAT`。
   5. CompoaseShader  混合着色   `PorterDuff.Mode` 一共有 17 个,[参考地址](https://juejin.im/post/596baf5f6fb9a06bb15a3df9) 以分为两类：
      1. Alpha 合成 (Alpha Compositing)
      2. 混合 (Blending)

9. setColorFiler  颜色过滤

   1. LightingColorFilter   参数里的 `mul` 和 `add` 都是和颜色值格式相同的 int 值，其中 `mul` 用来和目标像素相乘，`add` 用来和目标像素相加：

      ```
      ColorFilter lightingColorFilter = new LightingColorFilter(0x00ffff, 0x000000);
      paint.setColorFilter(lightingColorFilter);
      ```

   2. PorterDuffColorFilter 这个 `PorterDuffColorFilter` 的作用是使用一个指定的颜色和一种指定的 `PorterDuff.Mode` 来与绘制对象进行合成。它的构造方法是 `PorterDuffColorFilter(int color, PorterDuff.Mode mode)` 其中的 `color` 参数是指定的颜色， `mode` 参数是指定的 `Mode`。同样也是 `PorterDuff.Mode` ，不过和 `ComposeShader` 不同的是，`PorterDuffColorFilter` 作为一个 `ColorFilter`，只能指定一种颜色作为源，而不是一个 `Bitmap`。

   3. ColorMatrixColorFilter 

10. setXferMode   要绘制的内容和目标点的内容结合生成新的图形  显示   ,要结合离屏缓存技术
   1. 离屏缓存   `Canvas.saveLayer()`
   2. 离屏缓存  `View.setLayerType()`
   3. 控制好透明区域  PorterDuff.Mode.DST_IN

11. setDither 设置图像抖动  色彩优化  小图上使用 解决降低色彩深度时的模糊

12. setFilterBitmap  设置双线性过滤  降低颗粒度  更柔和

13. setPathEffect  设置图形轮廓效果 

    1. CornerPathEffect  所有拐角变圆角
    2. DisCretePtahEffect  将线条随机偏离轨道 实现不规则的边界显示
    3. DashPathEffect  边界虚线
    4. PathDashPathEffect   可以传入一个path 生成的图形来绘制  边界显示  第二个参数是头与头之间的距离   第三个参数是 随机轨道便宜   第四个是拐弯的时候path的图形变换   TRANSLATE 平移  ROTATE 旋转 MORPH 变体
    5. SumPathEffect 组合效果 可以组合上边的效果同时作用于绘制上 区分于ComposePathEffect  是每次的轮廓都是局域原始的图形
    6. ComposePathEffect   组合效果区分于SumPathEffect  每次的效果是基于当前图形的

14. setShadowLayer 在下方加 阴影绘制  传入阴影的模糊范围以及偏移量   只作用文字绘制其他形式需要关闭硬件加速,如果传入color是半透明的   会用自己的透明度 如果不是就会使用paint的透明度

15. setMaskFilter  在上方附加绘制效果

    1. BlurMaskFilter   传入模糊范围以及模糊形式     NOMAL 那内外都模糊  SOLID 内部正常绘制 外部模糊  INNER 内部模糊外部不模糊 OUTER 内部不绘制 外部模糊 
    2. EmbossMaskFilter 浮雕效果 参数一 三元数组指定光源方向  参数二 光强0-1  参数三 炫光系数  参数四 应用光线范围

16. getFillPath  获取实际路径path 

17. getTextPath

18. reset   重置paint

19. set()  复制另一个paint

20. setFlags  多属性设置

21. setTypeFace 设置字体  样式

    ```
    paint.setTypeface(Typeface.createFromAsset(getContext().getAssets(), "Satisfy-Regular.ttf"));
    ```

22. setFakeBoldText 设置伪粗体

23. setStrikeThrurtext  加删除线

24. setUnderLineText  下滑 先

25. setTextSkewX  设置横向错切角度   倾斜字体

26. setTextScaleX  文字横向缩放

27. setLetterSpacing  字符间距

28. setFontFeatureSetting  设置css样式设置文字

    ```
    paint.setFontFeatureSettings("smcp"); // 设置 "small caps"
    canvas.drawText("Hello HenCoder", 100, 150, paint);
    ```

29. setTextAlign 设置对其方式  

30. setTextLocale   区域字体  中国 台湾  ....

31. setHinting  字体微调

32. setSubpixeText  次像素抗锯齿

33. getFontSpacing  获取行间距  基线

34. FontMetircs   行间距   不局限于baseline

35. getTextBound  文字显示范围  不包含前后的小间距

36. measureText  测量文字宽度 包含前后间距

37. getTextWidth  获取字符的宽度

38. breakText  获取字符宽度  有限定宽度时会截断

39.  getRunAdvace  某个字距离光标的最小距离  要注意emoji显示是一个 但是占位是多个

40. getOffsetForAdvance   文字中最接近某个像素位置的字符

41. hasGlyph   是否是单独字形  emoji是4个

#### 绘制顺序

1.  draw()  协调管理调用    drawBackGround(绘制背景,不可重写)   onDraw(绘制主题)  dispatchDraw  (绘制子控件) onDrawForeground(绘制滑动相关的前景色)

   1. 在 `ViewGroup` 的子类中重写除 `dispatchDraw()` 以外的绘制方法时，可能需要调用 `setWillNotDraw(false)`；切换完整的绘制流程

   2. 在重写的方法有多个选择时，优先选择 `onDraw()`。

#### 动画

总体分三类  补间动画  帧动画  属性动画  目前所有动画基本都在使用不能够属性动画

1. 帧动画  是图片的定时切换  耗费内存 耗费资源
2. 补间动画 扩展性差  可以用xml  也可以用代码是想  只有固定几个动画可以使用
3. 属性动画 扩展性好  使用灵活
4. view动画  直接用控件的自由挨批实现动画



1. view.animate()  后面可以设置动画类型  平移旋转  搜房透明 以及  作用方向等等

2. 属性动画PropertyAnimator   使用时直接调用ObjectAnimator  传入动画字符串类型  ,属性动画又分为ValueAnimator  是一个数据值的变化  传入监听在监听里对控件的快高透明度做变化达到动画的效果 ObjectAnimator 只是封装了ValueAnimtor  使使用更简单



   1. AccelerateDecelerateInterpolator  先加速再减速
   2. LinearInterpolator 匀速
   3. AccelerateInterpolator持续加速
   4. DecelerateInterpolator持续减速到0
   5. AnticipateInterpolator 先回啦再正常轨迹运行
   6. OvershootInterpolator 超过终点回弹
   7. AnticipateOvershootInterpolator 先回拉  然后超过终点回弹
   8. BounceInterpolator 在终点弹跳
   9. CycleInterpolator 正弦 余弦曲线 可自定义曲线周期
   10. PathInterpolator 自定义时间完成度   动画完成度
   11. FastOutLinearInInterpolator 加速运动
   12. FastOutSlowInInterpolator 先加速再减速
   13. LinearOutSlowInInterpolator 持续减速

3. TypeEvaluator  差值器

4. ArgbEvaluator  颜色差值器

5. 自定义Evaluator  

6. ofObject+ Evaluator  可以不限定类型做动画

7. PropertyValuesHolder  同一个动画中改变多个属性 ,参数里只能有一个数值 类似 animatorSet   不过animatorSet  数值改变比较灵活

8. PropertyValuesHolder.ofKeyframe  把一个动画拆分 显示  时间和动画完成度的更改

   ```
   // 在 0% 处开始
   Keyframe keyframe1 = Keyframe.ofFloat(0, 0);
   // 时间经过 50% 的时候，动画完成度 100%
   Keyframe keyframe2 = Keyframe.ofFloat(0.5f, 100);
   // 时间见过 100% 的时候，动画完成度倒退到 80%，即反弹 20%
   Keyframe keyframe3 = Keyframe.ofFloat(1, 80);
   PropertyValuesHolder holder = PropertyValuesHolder.ofKeyframe("progress", keyframe1, keyframe2, keyframe3);
   
   ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(view, holder);
   animator.start();
   
   ```

   自定义动画  属性动画之所以能够执行是因为 在控件中有get/set方法  这个方法所对应的字段在draw()里绘制时有使用 所以不断绘制来做出动画效果