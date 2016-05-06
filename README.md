# DrawFirstBoke

生活是一面镜子，你对它笑，它就对你笑；你对它哭，它也对你哭。——萨克雷

在正文开始之前，我先抛一个脑洞大开的题目给大家：商人以45元一双进购了2双鞋子，然后亏本30一双出售。某个顾客给了他100买了2双鞋子，商人没零钱找于是拿着这100找邻居换了100的零钱，后来邻居发现这100是假的，商人只得陪了邻居100真的。。。 请问商人亏了多少？？

相关文章：

[Android自定义View之Path解析](http://blog.csdn.net/u012551350/article/details/51245793)

##一、Paint与Canvas

绘图需要两个工具，笔和纸。这里的 `Paint`相当于笔，而 `Canvas`相当于纸，不过需要注意的是 `Canvas`（画布）无限大，没有边界，切记理解成只有屏幕大小。我这里打个比方， `Canvas`是整个天空，而屏幕是通过窗户看到的景色。

那么我需要改变画笔大小，粗细，颜色，透明度，字体样式等都需要在 `Paint`里面设置；同样要画出圆形，矩形，不规则形状都是在 `Canvas`里面操作的。

###Paint

####Paint的基本设置函数

 1. `mPaint.setAntiAlias(true) //设置是否抗锯齿;`
 2. `mPaint.setStyle(Paint.Style.FILL_AND_STROKE); //设置填充样式`
 3. `mPaint.setColor(Color.GREEN);//设置画笔颜色`
 4. `mPaint.setStrokeWidth(2);//设置画笔宽度`
 5. `mPaint.setShadowLayer(10,15,15,Color.RED);//设置阴影`

####1 、setAntiAlias(true)  设置是否抗锯齿

设置抗锯齿会使图像边缘更清晰一些，锯齿痕迹不会那么明显。        
        
![paint](http://img.blog.csdn.net/20160505172533656)  

####2、setStyle (Paint.Style style)  设置填充样式

`Paint.Style` 类型：

`Paint.Style.FILL_AND_STROKE`     填充且描边
`Paint.Style.STROKE`     描边
`Paint.Style.FILL`     填充

看下上面三种类型，这里以矩形为例：

![paint](http://img.blog.csdn.net/20160505173932002)

上图可见，`FILL`与`FILL_AND_STROKE`效果上没什么区别。

####3、setColor(@ColorInt int color)   设置画笔颜色

这个方法比较简单，这里就不再累诉。

####4、setStrokeWidth(float width)  设置画笔宽度

这个方法也比较简单，略。。。

####5、setShadowLayer(float radius, float dx, float dy, int shadowColor)     设置阴影

先来看看参数代表的含义：

radius ： 表示阴影的倾斜度
dx       ： 水平位移
dy        :   垂直位移
shadowColor    :   阴影颜色

看一个简单的例子：

```
 paint.setShadowLayer(5,10,10,Color.parseColor("#abc133"));
```

效果图：

![paint](http://img.blog.csdn.net/20160505223930384)

这里你可能有疑问，为啥我自己演示了一篇却看不到矩形，圆形等图形的阴影，只能看到文本的阴影呢？那么我们需要注意的是：**这个方法不支持硬件加速，所以我们要测试时必须先关闭硬件加速。**

那么请加上`setLayerType(LAYER_TYPE_SOFTWARE, null);` 并且确保你的最小`api8`以上。

###Canvas

画布背景设置：

```
canvas.drawColor(Color.BLUE);
anvas.drawRGB(255, 255, 0);  
```
 这两个功能一样，都是用来设置背景颜色的。

我们只需要重写`onDraw(Canvas canvas)`方法，就可以绘制你想要的图形了。

```
    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
         //绘制图形
        }
```

##二、基本几何图形绘制

###1、画直线

方法预览：

```
drawLine(float startX, float startY, float stopX, float stopY,
            @NonNull Paint paint)
```

参数：

startX ： 开始点X坐标
startY ： 开始点Y坐标
stopX ： 结束点X坐标 
stopY ： 结束点Y坐标

```
paint.setStyle(Paint.Style.FILL);
paint.setStrokeWidth(5);    
paint.setColor(Color.parseColor("#FF0000"));
canvas.drawLine(100,100,600,600,paint);
```

![paint](http://img.blog.csdn.net/20160505232526351)

###2、多条直线

方法预览：

```

drawLines(@Size(min=4,multiple=2) float[] pts, int offset, int count, Paint paint)

drawLines(@Size(min=4,multiple=2) @NonNull float[] pts, @NonNull Paint paint)

```

参数：

pts : 是点的集合且**大小最小为4而且是2的倍数**。表示每2个点连接形成一条直线，pts 的组织方式为{x1,y1,x2,y2....}
offset : 集合中跳过的数值个数，注意不是点的个数！一个点是两个数值
count : 参与绘制的数值的个数，指pts[]里数值个数，而不是点的个数，因为一个点是两个数值

还是来看个例子：

```
        float [] pts={50,50,200,200,400,400,600,600};
        canvas.drawLines(pts,paint);
```

点（50，50）和点（200，200）连接成一条直线；点（400，400）和点（600，600）连接成直线。

![paint](http://img.blog.csdn.net/20160505233710162)

再来看下面这个例子：

```
        float [] pts={50,50,200,200,400,400,600,600};
        canvas.drawLines(pts,1,4,paint);
```

表示从第二个50开始连续的4个点（50，200，200，400）连接的直线

![paint](http://img.blog.csdn.net/20160505234934570)

###3、点及多个点

方法预览：

```

drawPoint(float x, float y, @NonNull Paint paint)

drawPoints(@Size(multiple=2) @NonNull float[] pts, @NonNull Paint paint)

drawPoints(@Size(multiple=2) float[] pts, int offset, int count,
            @NonNull Paint paint)

```

点的绘制和上面直线的绘制一样，我这里就不再累诉了。

###4、矩形

方法预览：

```
drawRect(@NonNull RectF rect, @NonNull Paint paint)

drawRect(@NonNull Rect r, @NonNull Paint paint)

drawRect(float left, float top, float right, float bottom, @NonNull Paint paint)

```

区别`RectF` 与`Rect` ，`RectF`坐标系是浮点型；`Rect`坐标系是整形。

圆角矩形方法预览：

```
drawRoundRect(@NonNull RectF rect, float rx, float ry, @NonNull Paint paint)

drawRoundRect(float left, float top, float right, float bottom, float rx, float ry,
            @NonNull Paint paint)
            
```

参数：

RectF  ：  绘制的矩形
rx        ：  生成圆角的椭圆X轴半径
ry        ：  生成圆角的椭圆Y轴的半径

![paint](http://img.blog.csdn.net/20160506000555904)

###5、圆形

方法预览：

```
drawCircle(float cx, float cy, float radius, @NonNull Paint paint)

```

参数：

cx          ：  圆心X坐标
cy          ：  圆心Y坐标
radius   ：  半径

```
canvas.drawCircle(400,400,300,paint);
```

![paint](http://img.blog.csdn.net/20160506001057031)

###6、椭圆

方法预览：

```
drawOval(@NonNull RectF oval, @NonNull Paint paint)

drawOval(float left, float top, float right, float bottom, @NonNull Paint paint)

```

![paint](http://img.blog.csdn.net/20160506001436697)

###7、圆弧

方法预览：

```
drawArc(@NonNull RectF oval, float startAngle, float sweepAngle, boolean useCenter,
            @NonNull Paint paint)

drawArc(float left, float top, float right, float bottom, float startAngle,
            float sweepAngle, boolean useCenter, @NonNull Paint paint)
```
参数：

oval               ：   生成椭圆的矩形
startAngle     ：   弧开始的角度  （X轴正方向为0度，顺时针弧度增大）
sweepAngle   ：  绘制多少弧度  （注意不是结束弧度）
useCenter       ：  是否有弧的两边  true有两边  false无两边

画笔设置填充：

```
        paint.setStyle(Paint.Style.FILL);
        RectF rect=new RectF(100,100,800,600);
        canvas.drawArc(rect,0,90,false,paint);

        RectF rf=new RectF(100,600,800,1100);
        canvas.drawArc(rf,0,90,true,paint);
```

![paint](http://img.blog.csdn.net/20160506002503365)

画笔设置描边：

```
        paint.setStyle(Paint.Style.STROKE);
        RectF rect=new RectF(100,100,800,600);
        canvas.drawArc(rect,0,90,false,paint);

        RectF rf=new RectF(100,600,800,1100);
        canvas.drawArc(rf,0,90,true,paint);
```

![paint](http://img.blog.csdn.net/20160506002706352)

基础图形的绘制到此差不多结束了。

